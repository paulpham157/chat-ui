<script lang="ts">
	import { browser } from "$app/environment";
	import { beforeNavigate } from "$app/navigation";
	import { base } from "$app/paths";
	import { page } from "$app/state";
	import IconNew from "$lib/components/icons/IconNew.svelte";
	import { createEventDispatcher } from "svelte";
	import CarbonClose from "~icons/carbon/close";
	import CarbonTextAlignJustify from "~icons/carbon/text-align-justify";

	interface Props {
		isOpen?: boolean;
		title: string | undefined;
		children?: import("svelte").Snippet;
	}

	let { isOpen = false, title = $bindable(), children }: Props = $props();

	let closeEl: HTMLButtonElement | undefined = $state();
	let openEl: HTMLButtonElement | undefined = $state();

	$effect(() => {
		title ??= "New Chat";
	});

	const dispatch = createEventDispatcher();
	beforeNavigate(() => {
		dispatch("toggle", false);
	});

	let shouldFocusClose = $derived(isOpen && closeEl);
	let shouldRefocusOpen = $derived(!isOpen && browser && document.activeElement === closeEl);
	$effect(() => {
		if (shouldFocusClose) {
			closeEl?.focus();
		} else if (shouldRefocusOpen) {
			openEl?.focus();
		}
	});
</script>

<nav
	class="flex h-12 items-center justify-between border-b bg-gray-50 px-3 dark:border-gray-800 dark:bg-gray-800/70 md:hidden"
>
	<button
		type="button"
		class="-ml-3 flex size-12 shrink-0 items-center justify-center text-lg"
		onclick={() => dispatch("toggle", true)}
		aria-label="Open menu"
		bind:this={openEl}><CarbonTextAlignJustify /></button
	>
	<div class="flex h-full items-center justify-center">
		<span class="truncate px-4" data-testid="chat-title">{title}</span>
	</div>
	<a
		class:invisible={!page.params?.id}
		href="{base}/"
		class="-mr-3 flex size-12 shrink-0 items-center justify-center text-lg"><IconNew /></a
	>
</nav>
<nav
	class="fixed inset-0 z-30 grid max-h-screen grid-cols-1 grid-rows-[auto,auto,1fr,auto] bg-white dark:bg-gray-900 {isOpen
		? 'block'
		: 'hidden'}"
>
	<div class="flex h-12 items-center px-4">
		<button
			type="button"
			class="-mr-3 ml-auto flex size-12 items-center justify-center text-lg"
			onclick={() => dispatch("toggle", false)}
			aria-label="Close menu"
			bind:this={closeEl}><CarbonClose /></button
		>
	</div>
	{@render children?.()}
</nav>
