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
	import { goto } from '$app/navigation';

	let Name: string;
	let UseEvri = false;
	let Description = '';
	let Courier = '';

	let internal_name = ';';

	let ErrorMessage = 'Something Went Wrong.';
	let HasErrored = false;
	let submitting = false;

	async function handleSubmit() {
		submitting = true;
		HasErrored = false;
		ErrorMessage = '';

		if (!Name || !Name.trim()) {
			submitting = false;
			HasErrored = true;
			ErrorMessage = 'Add a name!';
			return;
		}

		try {
			const res = await fetch('https://evribackend.between-my-legs-there-is-a.monster/submit', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({
					name: Name.trim(),
					description: Description?.trim() || null,
					uses_evri: !!UseEvri,
					courier: Courier?.trim() || null
				})
			});

			let payload: any = null;

			try {
				payload = await res.json();
			} catch {
				HasErrored = true;
				ErrorMessage = 'Something went wrong!!!';
			}

			if (!res.ok) {
				HasErrored = true;
				ErrorMessage = payload?.error || `error while submission: (${res.status})`;
				return;
			}

			internal_name = Name.trim().toLowerCase();
			await new Promise((resolve) => setTimeout(resolve, 250));
			goto(`/company/${internal_name}`);
		} catch (err) {
			HasErrored = true;
			ErrorMessage = 'something went wrong!!!';
		} finally {
			submitting = false;
		}
	}
</script>

{#if open}
	<div
		class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm"
		transition:fly
	>
		<Card.Root class="w-full max-w-sm ">
			<Card.Header>
				<Card.Title>Submit a Company</Card.Title>

				<Card.Description
					>Submit a company to the list, whether they use Evri or not!</Card.Description
				>
			</Card.Header>

			<Card.Content>
				<form>
					<div class="flex flex-col gap-6">
						<div class="grid gap-2">
							<Label for="name">Name</Label>
							<Input id="name" placeholder="Amazon" autocomplete="off" required bind:value={Name} />
						</div>
						<div class="grid gap-2">
							<div class="flex items-center">
								<Label for="description">Description</Label>
							</div>
							<Textarea
								id="description"
								bind:value={Description}
								placeholder="Describe the company, and their relationship with Evri. Include whether they offer other options, such as Royal Mail or Yodel."
								autocomplete="off"
								rows={4}
							/>
						</div>
						<!-- unfinished
							{#if !UseEvri}
								<div class="grid gap-2">
									<Label for="name">Courier</Label>
									<Input
										id="name"
										placeholder="Royal Mail"
										autocomplete="off"
										required
										bind:value={Courier}
									/>
								</div>
							{/if}
						-->

						<div class="flex items-center gap-2">
							<Switch id="evritoggle" bind:checked={UseEvri} />
							<label for="evritoggle">Do they use Evri?</label>
						</div>
					</div>
				</form>
			</Card.Content>

			<Card.Footer class="flex-col gap-2">
				{#if HasErrored}
					<div class="error m2 mb-2 w-full max-w-sm">
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
