<script lang="ts">
	import { Button } from 'flowbite-svelte';
	import Editor from '$lib/components/editor/editor.svelte';
	import markdown2confluence from '@shogobg/markdown2confluence';
	import type * as Monaco from 'monaco-editor/esm/vs/editor/editor.api';

	let covert: () => void;
	let setValue: (value: string) => void;

	const editorOptions: Monaco.editor.IStandaloneEditorConstructionOptions = {
		minimap: {
			enabled: false
		},
		wordWrap: 'on'
	};

	const init = ({ detail: { getValue } }: CustomEvent) => {
		covert = async () => {
			const text = getValue();
			const result = markdown2confluence(text);

			try {
				setValue(result);
			} catch (error: any) {
				console.error(error?.message);
			}
		};
	};

	const initOutput = ({ detail }: CustomEvent) => {
		setValue = detail.setValue;
	};
</script>

<svelte:head>
	<title>Transform markdown to jira</title>
	<meta name="description" content="Transform markdown to jira" />
	<meta name="keywords" content="markdown,confluence,jira" />
</svelte:head>

<div>
	<div class="container">
		<div class="input">
			<Editor options={editorOptions} defaultText={'# Input here'} on:init={init} />
		</div>
		<div class="output">
			<Editor defaultText={'# Will output to here'} options={editorOptions} on:init={initOutput} />
		</div>
	</div>

	<div class="fixed bottom-10 right-10 z-10">
		<Button on:click={covert} color="blue">Convert</Button>
	</div>
</div>

<style>
	.container {
		height: 100vh;
		display: flex;
	}

	.container > div {
		flex: 1;
	}

	.container > div + div {
		border-left: 1px solid #ccc;
	}
</style>
