<script lang="ts">
	import type * as Monaco from 'monaco-editor/esm/vs/editor/editor.api';
	import { createEventDispatcher, onDestroy, onMount } from 'svelte';

	export let defaultText = '';
	export let options: Monaco.editor.IEditorOptions = {};

	let editorContainer: HTMLElement;
	let monaco: typeof Monaco;
	let editor: Monaco.editor.IStandaloneCodeEditor;

	const dispatch = createEventDispatcher();

	const resize = (editor: Monaco.editor.IStandaloneCodeEditor, wrapElement: HTMLElement) => {
		editor.layout({ width: 0, height: 0 });

		window.requestAnimationFrame(() => {
			const rect = wrapElement.getBoundingClientRect();

			editor.layout({ width: rect.width, height: rect.height });
		});
	};

	onMount(async () => {
		// Import our 'monaco.ts' file here
		// (onMount() will only be executed in the browser, which is what we want)
		monaco = (await import('./monaco')).default;

		// Your monaco instance is ready, let's display some code!
		editor = monaco.editor.create(editorContainer, options);
		const model = monaco.editor.createModel(defaultText, 'markdown');

		editor.setModel(model);

		dispatch('init', {
			editor,
			model,
			getValue: () => {
				return editor.getValue();
			},
			setValue: (value: string) => {
				editor.setValue(value);
			},
			resize: () => {
				resize(editor, editorContainer);
			}
		});
	});

	onDestroy(() => {
		monaco?.editor.getModels().forEach((model) => model.dispose());
		editor?.dispose();
	});
</script>

<div class="container" bind:this={editorContainer} />

<style>
	.container {
		width: 100%;
		height: 100%;
	}
</style>
