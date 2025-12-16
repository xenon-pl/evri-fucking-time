<script lang="ts">
	import './layout.css';
	import { ModeWatcher } from 'mode-watcher';
	import favicon from '$lib/assets/favicon.svg';
	import Header from '$lib/components/ui/xenon/Header.svelte';
	import { Toaster } from 'svelte-sonner';

	let { children } = $props();
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>
<ModeWatcher />
<Toaster theme="dark" class="bg-card text-left" />
<div class=" flex min-h-[100svh] flex-col ">
	{@render children()}
</div>
