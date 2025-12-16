<script lang="ts">
	export let open = false;
	export let comment_id = -1;
	export let comment_text = '';

	import { AlertCircleIcon, CheckCircleIcon } from '@lucide/svelte';
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
	import * as NativeSelect from '$lib/components/ui/native-select/index.js';

	let Name: string;
	let UseEvri = false;
	export let Comment = '';
	let Courier = '';
	let report = '';
	let internal_name = '';

	let ErrorMessage = 'Something Went Wrong.';
	let HasErrored = false;
	let submitting = false;

	let reason = '';
	let success = false;

	async function handleSubmit() {
		submitting = true;
		HasErrored = false;

		const res = await fetch(
			'https://evribackend.between-my-legs-there-is-a.monster/comment_reports',
			{
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({
					comment_id: comment_id!,
					message: report!,
					reason: reason!
				})
			}
		);
		await new Promise((resolve) => setTimeout(resolve, 250));
		if (!res.ok) {
			success = false;
			HasErrored = true;
			let ErrorMessage = 'Something Went Wrong.';
			console.log('shit fucked');
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
		<Card.Root class="w-full max-w-sm ">
			<Card.Header>
				<Card.Title>Report this comment</Card.Title>
				<Card.Description>"{Comment}", ID: {comment_id}</Card.Description>
			</Card.Header>
			<Card.Content>
				<form on:submit|preventDefault={handleSubmit}>
					<div class="flex flex-col gap-6">
						<div class="grid gap-2">
							<Label for="name">Reason</Label>
							<NativeSelect.Root bind:value={reason}>
								<NativeSelect.Option value="bigotry"
									>Racism, Homophobia or other bigotry</NativeSelect.Option
								>
								<NativeSelect.Option value="spam">Spam / Ads</NativeSelect.Option>
								<NativeSelect.Option value="harrasment"
									>Harrasment, Threats, etc</NativeSelect.Option
								>
								<NativeSelect.Option value="other" disabled>Other</NativeSelect.Option>
							</NativeSelect.Root>
						</div>
						<div class="grid gap-2">
							<div class="flex items-center">
								<Label for="comment">Details</Label>
							</div>
							<Textarea
								id="comment"
								bind:value={report}
								placeholder="What's wrong? Be detailed, the more in detail you go the more likely the comment is to get deleted"
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
