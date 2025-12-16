<script lang="ts">
	export let open = false;
	import { AlertCircleIcon } from '@lucide/svelte';
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Label } from '$lib/components/ui/label/index.js';
	import { Textarea } from '$lib/components/ui/textarea/index.js';
	import { Switch } from '$lib/components/ui/switch/index.js';
	import * as Alert from '$lib/components/ui/alert/index.js';
	import * as Card from '$lib/components/ui/card/index.js';
	import { fly } from 'svelte/transition';

	let UseEvri = false;

	let ErrorMessage = 'An Unknown Error Occurred.';
	let HasErrored = false;
	let submitting = false;

	async function handleSubmit() {
		submitting = true;
		HasErrored = false;
	}
</script>

{#if open}
	<div
		class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm"
		transition:fly
	>
		<Card.Root class="w-full max-w-sm text-center">
			<Card.Content>
				<p class="emojifirst text-6xl">🖕</p>
				<p class="mt-3 text-2xl font-bold">Evri <span class="text-red-400">Fucking</span> Time</p>
				<p class="m-0 text-muted-foreground">v2.0.0</p>
				<p class="m-0 text-muted-foreground">
					by <a href="https://xenon.zone" class="underline">🦌xenon</a>
				</p>
			</Card.Content>

			<Card.Footer class="flex-col gap-2">
				<Button
					class="w-full"
					onclick={() => {
						open = false;
					}}>Ok</Button
				>
			</Card.Footer>
		</Card.Root>
	</div>
{/if}
