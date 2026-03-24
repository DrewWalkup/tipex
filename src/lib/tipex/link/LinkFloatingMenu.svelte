<script lang="ts" module>
	import type { TipexEditor } from '../Tipex.svelte';

	export interface LinkFloatingMenuProps {
		floatingRef: HTMLDivElement | undefined;
		tipex: TipexEditor;
	}
</script>

<script lang="ts">
	import { fade } from 'svelte/transition';
	import { Editor } from '@tiptap/core';
	import { onMount } from 'svelte';
	import Fa6SolidXmark from '../icons/Fa6SolidXmark.svelte';
	import Fa6SolidCheck from '../icons/Fa6SolidCheck.svelte';
	import Fa6SolidArrowUpRightFromSquare from '../icons/Fa6SolidArrowUpRightFromSquare.svelte';

	let { floatingRef = $bindable(), tipex }: LinkFloatingMenuProps = $props();

	function handleAcceptLink() {
		if (tipex instanceof Editor) {
			tipex
				.chain()
				.focus(tipex.state.selection.$anchor.pos - tipex.state.selection.$anchor.parentOffset)
				.run();
		}
	}

	function handleCancelLink() {
		if (tipex instanceof Editor) {
			tipex.chain().focus().unsetLink().run();
		}
	}

	function handleOpenLink() {
		if (tipex instanceof Editor) {
			const href = tipex.getAttributes('link').href;
			// Only allow http(s) — mailto/tel/relative paths aren't meaningful in a popup
			if (typeof href === 'string' && /^https?:\/\//i.test(href)) {
				window.open(href, 'popup', `width=700,height=900,location=0,top=0,right=0`);
			}
		}
	}

	let hideAnchorControl = $state(true);

	const computedStyleString = $derived(`display: ${hideAnchorControl ? 'none' : 'flex'}`);

	onMount(() => {
		hideAnchorControl = false;
	});
</script>

<div
	class="tipex-floating-group"
	bind:this={floatingRef}
	style={computedStyleString}
	transition:fade
>
	<button
		type="button"
		class="tipex-floating-button"
		onclick={handleOpenLink}
		aria-label="Open link in new tab"
	>
		<Fa6SolidArrowUpRightFromSquare class="h-3 w-3" />
	</button>
	<button
		type="button"
		class="tipex-floating-button"
		onclick={handleAcceptLink}
		aria-label="Accept link"
	>
		<Fa6SolidCheck class="h-3 w-3" />
	</button>
	<button
		type="button"
		class="tipex-floating-button"
		onclick={handleCancelLink}
		aria-label="Cancel link"
	>
		<Fa6SolidXmark class="h-3 w-3" />
	</button>
</div>
