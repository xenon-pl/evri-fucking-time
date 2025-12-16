<script lang="ts">
	export let open = false;

	export let company_id = -1;
	export let company_name = '';
	export let company_internal_name = '';

	import { AlertCircleIcon, CheckCircleIcon } from '@lucide/svelte';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Label } from '$lib/components/ui/label/index.js';
	import { Textarea } from '$lib/components/ui/textarea/index.js';
	import * as Alert from '$lib/components/ui/alert/index.js';
	import * as Card from '$lib/components/ui/card/index.js';
	import { fly } from 'svelte/transition';
	import * as NativeSelect from '$lib/components/ui/native-select/index.js';

	let report = '';
	let reason = '';
	let ErrorMessage = 'Something Went Wrong.';
	let HasErrored = false;
	let submitting = false;
	let success = false;

	async function handleSubmit() {
		submitting = true;
		HasErrored = false;

		const res = await fetch(
			'https://evribackend.between-my-legs-there-is-a.monster/company_reports',
			{
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({
					company_id: company_id!,
					reason: reason!,
					message: report!
				})
			}
		);

		await new Promise((resolve) => setTimeout(resolve, 250));

		if (!res.ok) {
			success = false;
			HasErrored = true;
			ErrorMessage = 'Something Went Wrong.';
			submitting = false;
			return;
		}

		success = true;
		submitting = false;
		open = false;
		ErrorMessage = 'Successfully reported!';
	}
</script>

{#if open}
	<div
		class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm"
		transition:fly
	>
		<Card.Root class="w-full max-w-sm">
			<Card.Header>
				<Card.Title>Report this company</Card.Title>
				<Card.Description>
					"{company_name}" ({company_internal_name}), ID: {company_id}
				</Card.Description>
			</Card.Header>

			<Card.Content>
				<form on:submit|preventDefault={handleSubmit}>
					<div class="flex flex-col gap-6">
						<div class="grid gap-2">
							<Label for="reason">Reason</Label>
							<NativeSelect.Root bind:value={reason}>
								<NativeSelect.Option value="uses_evri">"Uses Evri" Status</NativeSelect.Option>
								<NativeSelect.Option value="wrong_info">Description Is Wrong</NativeSelect.Option>
								<NativeSelect.Option value="duplicate">Duplicate Listing</NativeSelect.Option>
								<NativeSelect.Option value="other">Other</NativeSelect.Option>
							</NativeSelect.Root>
						</div>

						<div class="grid gap-2">
							<div class="flex items-center">
								<Label for="details">Details</Label>
							</div>
							<Textarea
								id="details"
								bind:value={report}
								placeholder="What's wrong? Be descriptive, the more descriptive you are, the more likely it is it will be fixed"
							/>
						</div>
					</div>
				</form>
			</Card.Content>

			<Card.Footer class="flex-col gap-2">
				{#if HasErrored}
					<div class="error m2 mb-2 w-full">
						<Alert.Root variant="destructive">
							{#if success}
								<CheckCircleIcon />
							{:else}
								<AlertCircleIcon />
							{/if}
							<Alert.Title>{ErrorMessage}</Alert.Title>
						</Alert.Root>
					</div>
				{/if}

				<Button
					type="submit"
					class="w-full"
					disabled={submitting || !reason || !report || company_id < 0}
					onclick={() => {
						handleSubmit();
					}}
				>
					{#if submitting}
						Submitting...
					{:else}
						Submit
					{/if}
				</Button>

				<Button
					variant="outline"
					class="w-full"
					onclick={() => {
						open = false;
					}}
				>
					Cancel
				</Button>
			</Card.Footer>
		</Card.Root>
	</div>
{/if}
