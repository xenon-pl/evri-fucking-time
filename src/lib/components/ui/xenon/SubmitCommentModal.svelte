<script lang="ts">
	export let open = false;
	export let company_id = -1;
	export let text = ';';

	import { AlertCircleIcon } from '@lucide/svelte';
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Label } from '$lib/components/ui/label/index.js';
	import { Textarea } from '$lib/components/ui/textarea/index.js';
	import { Switch } from '$lib/components/ui/switch/index.js';
	import * as Alert from '$lib/components/ui/alert/index.js';
	import * as Card from '$lib/components/ui/card/index.js';
	import { fly } from 'svelte/transition';
	import { goto } from '$app/navigation';
	import { invalidateAll } from '$app/navigation';
	let Name: string;
	let UseEvri = false;
	export let Comment = '';
	let Courier = '';

	let internal_name = ';';

	let ErrorMessage = 'Something Went Wrong.';
	let HasErrored = false;
	let submitting = false;

	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	async function handleSubmit() {
		submitting = true;
		HasErrored = false;

		if (Name! == null) {
			submitting = false;
			HasErrored = true;
			ErrorMessage = 'Add a name!';
			return;
		}

		const res = await fetch('https://evribackend.between-my-legs-there-is-a.monster/comments', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				name: Name!,
				text: Comment,
				company_id: company_id!
			})
		});
		internal_name = Name!.toLowerCase();
		await new Promise((resolve) => setTimeout(resolve, 250));
		if (!res.ok) {
			HasErrored = true;
			let ErrorMessage = 'Something Went Wrong.';
			submitting = false;
			return;
		}
		await invalidateAll();
		submitting = false;
		open = false;
		dispatch('submitted');
	}
</script>

{#if open}
	<div
		class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm"
		transition:fly
	>
		<Card.Root class="w-full max-w-sm ">
			<Card.Header>
				<Card.Title>Leave a comment</Card.Title>
			</Card.Header>
			<Card.Content>
				<form>
					<div class="flex flex-col gap-6">
						<div class="grid gap-2">
							<Label for="name">Your Name</Label>
							<Input
								id="name"
								placeholder="Anonymous"
								autocomplete="off"
								required
								bind:value={Name}
							/>
						</div>
						<div class="grid gap-2">
							<div class="flex items-center">
								<Label for="comment">Your Comment</Label>
							</div>
							<Textarea
								id="comment"
								placeholder="Your Comment! Be respectful."
								autocomplete="off"
								bind:value={Comment}
							/>
						</div>
					</div>
				</form>
			</Card.Content>

			<Card.Footer class="flex-col gap-2">
				{#if HasErrored}
					<div class="error m2 mb-2 w-full">
						<Alert.Root variant="destructive">
							<AlertCircleIcon />
							<Alert.Title>{ErrorMessage}</Alert.Title>
						</Alert.Root>
					</div>
				{/if}

				<Button
					type="submit"
					class="w-full"
					onclick={() => {
						handleSubmit();
					}}>Submit</Button
				>
				<Button
					variant="outline"
					class="w-full"
					onclick={() => {
						open = false;
					}}>Cancel</Button
				>
			</Card.Footer>
		</Card.Root>
	</div>
{/if}
