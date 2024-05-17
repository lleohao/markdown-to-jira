<script lang="ts">
	import Editor from '$lib/components/editor/editor.svelte';
	import { Button, ButtonGroup } from 'flowbite-svelte';
	import { onMount } from 'svelte';
	import _ from 'lodash';

	let setValue: (value: string) => void;
	let getValue: () => string;
	let resize: () => void;

	const initEditor = ({ detail }: CustomEvent) => {
		setValue = detail.setValue;
		getValue = detail.getValue;
		resize = detail.resize;

		// get the value from local storage
		const text = localStorage.getItem('markdown-cleaner');
		if (text) {
			setValue(text);
		}
	};

	const removeLink = () => {
		const text = getValue();
		const result = text.replace(/\[(.*?)\]\(.*?\)\.?/g, '$1');
		setValue(result);
	};

	const removeHighlight = () => {
		const text = getValue();
		const result = text.replace(/==(.*?)==/g, '$1');
		setValue(result);
	};

	const removeBold = () => {
		const text = getValue();
		const result = text.replace(/\*\*(.*?)\*\*/g, '$1').replace(/__(.*?)__/g, '$1');
		setValue(result);
	};

	const removeItalic = () => {
		const text = getValue();
		const result = text.replace(/\*(.*?)\*/g, '$1').replace(/_(.*?)_/g, '$1');
		setValue(result);
	};

	onMount(() => {
		const resizeHandler = _.debounce(() => {
			resize?.();
		}, 100);

		window.addEventListener('resize', resizeHandler);

		// save the current value to local storage
		window.addEventListener('beforeunload', () => {
			localStorage.setItem('markdown-cleaner', getValue());
		});
	});
</script>

<svelte:head>
	<title>Markdown Cleaner: Remove Links, Highlights & More Online</title>
	<meta
		name="description"
		content="Revamp your Markdown documents with our online cleaner that removes links, highlights, and other formatting with just a few clicks. Streamline your content for a cleaner, more focused read."
	/>
</svelte:head>

<div class="wrap">
	<Editor on:init={initEditor} options={{ minimap: { enabled: false }, wordWrap: 'on' }} />
	<div class="fixed bottom-10 right-10 z-10">
		<ButtonGroup>
			<Button on:click={removeLink}>Remove Link</Button>
			<Button on:click={removeHighlight}>Remove Highlights</Button>
			<Button on:click={removeBold}>Remove Bold</Button>
			<Button on:click={removeItalic}>Remove Italic</Button>
		</ButtonGroup>
	</div>
</div>

<style>
	.wrap {
		height: 100vh;
	}
</style>
