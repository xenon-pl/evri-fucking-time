<script lang="ts">
	import { Search, AlertCircleIcon } from '@lucide/svelte';
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Label } from '$lib/components/ui/label/index.js';
	import { Textarea } from '$lib/components/ui/textarea/index.js';
	import { Switch } from '$lib/components/ui/switch/index.js';
	import * as Alert from '$lib/components/ui/alert/index.js';
	import * as Card from '$lib/components/ui/card/index.js';
	import { fly } from 'svelte/transition';

	import Searchbar from '$lib/components/ui/xenon/Search.svelte';
	import SubmitModal from '$lib/components/ui/xenon/SubmitModal.svelte';
	import About from '$lib/components/ui/xenon/About.svelte';

	let UseEvri = false;
	let SubmitModalOpen = false;
	let AboutModalOpen = false;

	let ErrorMessage = 'An Unknown Error Occurred.';
	let HasErrored = false;
	let submitting = false;

	async function handleSubmit() {
		submitting = true;
		HasErrored = false;
	}
</script>

<p class="absolute right-0 left-0 m-5 text-center text-muted-foreground">
	🎉 2.0.0 - new website live!
</p>
<div
	class="page mx-auto flex max-w-2xl flex-1 flex-col items-center justify-center px-4 pb-[env(safe-area-inset-bottom)] text-center"
>
	<div class="m-1 flex items-baseline gap-2">
		<!-- svelte-ignore a11y-invalid-attribute -->
		<a
			class="text-4xl font-bold"
			role="button"
			tabindex="0"
			href="#"
			onclick={() => {
				AboutModalOpen = true;
			}}>Evri <span class="text-red-400">Fucking</span> Time.</a
		>
	</div>

	<p>An unofficial aggregator of retailers that utilize Evri</p>
	<Searchbar classes="mt-3" />
	<div class="mt-3 flex items-center gap-1">
		<Button
			size="sm"
			onclick={() => {
				SubmitModalOpen = true;
			}}>Submit a retailer</Button
		>
		<!--<Button size="sm" href="/companies" variant="secondary">All retailers</Button>-->
	</div>

	<div
		class="footer emojifirst absolute bottom-4 flex w-full justify-center text-sm text-muted-foreground"
	>
		<p>made with ♥ by <a href="https://xenon.zone" class="underline">🦌xenon</a></p>
	</div>
</div>

<!---->
<SubmitModal bind:open={SubmitModalOpen} />
<About bind:open={AboutModalOpen} />
