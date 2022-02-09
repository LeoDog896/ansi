<script lang="ts">
	import { onMount, onDestroy } from 'svelte'
	import { Editor, JSONContent } from '@tiptap/core'
	import StarterKit from '@tiptap/starter-kit'
	import Underline from "@tiptap/extension-underline"
	import TextStyle from '@tiptap/extension-text-style'
	import { Color } from '@tiptap/extension-color'
	import Placeholder from '@tiptap/extension-placeholder'
  
	let element
	let editor: Editor
	let data: JSONContent
  
	onMount(() => {
		editor = new Editor({
			onUpdate: ({ editor }) => {
   				data = editor.getJSON()
			},
			element: element,
			extensions: [
				StarterKit,
				Underline,
				Color,
				TextStyle,
				Placeholder.configure({
					placeholder: 'Write something...',
				})
			],
			content: '<p>Hello World!</p>',
			onTransaction: () => {
				// force re-render so `editor.isActive` works as expected
				editor = editor
			},
		})
	})
  
	onDestroy(() => {
		if (editor) {
			editor.destroy()
		}
	})
	</script>
  
{#if editor}
	<input
		type="color"
		on:input={event => editor.chain().focus().setColor(event.target.value).run()}
	/>
	<button on:click={() => editor.chain().focus().toggleBold().run()} class:active={editor.isActive('bold')}>
		B
	</button>
	<button on:click={() => editor.chain().focus().toggleItalic().run()} class:active={editor.isActive('italic')}>
		I
	</button>
	<button on:click={() => editor.chain().focus().toggleUnderline().run()} class:active={editor.isActive('underline')}>
		U
	</button>
	<button on:click={() => editor.chain().focus().toggleStrike().run()} class:active={editor.isActive('strike')}>
		S
	</button>
{/if}
  
<div class="w-screen h-1/2" bind:this={element} />
<div>
	{#if data && data.content}
		{#each data.content as line}
			{#each line.content as word}
				{word.text} {word.marks ? " -- " + word.marks.map(it => it.type).join(", ") : ""}
			{/each}
		{/each}
	{/if}
</div>

<style>

:global(.ProseMirror) {
	width: 100%;
	height: 100%;
}

button.active {
	background: black;
	color: white;
}
</style>