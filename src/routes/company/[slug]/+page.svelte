<script lang="ts">
	import Header from '$lib/components/ui/xenon/Header.svelte';
	import { AlertCircleIcon, SendIcon } from '@lucide/svelte';

	type Company = {
		id: number;
		internal_name: string;
		display_name: string;
		description: string;
		image_url: string;
		uses_evri: boolean;
	};

	type Comment = {
		id: number;
		company_id: number;
		text: string;
		username: string;
		likes: number;
		created_at: EpochTimeStamp;
	};

	let SubmitModalOpen = false;
	let ReportCommentModalOpen = false;
	let ReportProblemModalOpen = false;

	let HasErrored = false;
	let ErrorMessage = 'Something went wrong.';

	import * as Empty from '$lib/components/ui/empty/index.js';
	import { Button } from '$lib/components/ui/button/index.js';
	import { Input } from '$lib/components/ui/input/index.js';

	import { List, SearchX } from '@lucide/svelte';

	import { page } from '$app/stores';
	import { onMount } from 'svelte';

	import Search from '$lib/components/ui/xenon/Search.svelte';
	import SubmitCommentModal from '$lib/components/ui/xenon/SubmitCommentModal.svelte';
	import ReportCommentModal from '$lib/components/ui/xenon/ReportCommentModal.svelte';
	import ReportCompanyModal from '$lib/components/ui/xenon/ReportCompanyModal.svelte';

	import * as Card from '$lib/components/ui/card/index.js';
	import { fly } from 'svelte/transition';
	import { Label } from '$lib/components/ui/label';
	import { Textarea } from '$lib/components/ui/textarea';
	import * as Alert from '$lib/components/ui/alert/index.js';
	console.log($page.params);
	let slug = $page.params.slug;

	let loading = true;

	let timeout: ReturnType<typeof setTimeout>;

	let results: Company;
	let comments: Comment[];
	let CurrentComment = '';

	let ReportedCommentId: number | null = null;
	let ReportedCommentText = '';

	let error = false;
	async function fetchResults() {
		loading = true;
		const res = await fetch(
			`https://evribackend.between-my-legs-there-is-a.monster/companies?internal_name=eq.${slug!}`
		);
		results = (await res.json())[0] as Company;

		const rescomments = await fetch(
			`https://evribackend.between-my-legs-there-is-a.monster/comments?company_id=eq.${results.id!}`
		);
		comments = (await rescomments.json()) as Comment[];
		console.log(comments);
		loading = false;
	}

	// thanks chatgpt
	function timeAgo(input: number | string | Date) {
		const now = Date.now();

		let time: number;

		if (typeof input === 'string') {
			time = new Date(input).getTime();
		} else if (input instanceof Date) {
			time = input.getTime();
		} else {
			time = input < 1e12 ? input * 1000 : input;
		}

		if (Number.isNaN(time)) return 'unknown';

		const diff = now - time;

		if (diff < 0) return 'just now';

		const seconds = Math.floor(diff / 1000);
		const minutes = Math.floor(seconds / 60);
		const hours = Math.floor(minutes / 60);
		const days = Math.floor(hours / 24);

		const rtf = new Intl.RelativeTimeFormat('en', { numeric: 'auto' });

		if (seconds < 5) return 'just now';
		if (seconds < 60) return rtf.format(-seconds, 'second');
		if (minutes < 60) return rtf.format(-minutes, 'minute');
		if (hours < 24) return rtf.format(-hours, 'hour');
		if (days < 7) return rtf.format(-days, 'day');

		return new Date(time).toLocaleDateString('en-GB', {
			day: 'numeric',
			month: 'long',
			year: 'numeric'
		});
	}

	$: slug = $page.params.slug;

	$: if (slug) {
		fetchResults();
	}
</script>

<Header />
<Search classes="w-full p-6 pt-0" relclasses="w-full" />
{#if loading}
	<p>Loading...</p>
{:else if error}
	<div class="flex h-full items-center justify-center">
		<Empty.Root>
			<Empty.Header>
				<Empty.Media variant="icon" class="emojifirst">😿</Empty.Media>
				<Empty.Title>No Results</Empty.Title>
				<Empty.Description>
					"{slug}" does not exist.
				</Empty.Description>
			</Empty.Header>
		</Empty.Root>
	</div>
{:else}
	<div class="mx-6 mt-0 w-auto rounded-lg border bg-card p-6 text-left">
		<div class="flex flex-col gap-6">
			<div
				class="flex h-56 w-56 items-center justify-center rounded-lg border-1 bg-white p-6 text-gray-900"
			>
				<img
					src={results.image_url}
					alt={results.display_name}
					class="max-h-full max-w-full object-contain"
				/>
			</div>

			<div class="flex flex-col items-start gap-2 text-left">
				<h1 class="text-4xl font-bold">
					{results.display_name}
				</h1>
				<p class="text-sm text-muted-foreground">
					{results.description}
				</p>

				{#if results.uses_evri}
					<p class="flex items-center gap-2 text-red-400">
						<AlertCircleIcon class="h-5 w-5" />
						This company uses Evri.
					</p>
				{:else}
					<p class="flex items-center gap-2 text-green-400">
						<span class="text-base">✔</span>
						This company does not use Evri.
					</p>
				{/if}

				<Button
					class="items-right mt-2 text-right"
					onclick={() => {
						ReportProblemModalOpen = true;
					}}>Report a problem</Button
				>
			</div>
		</div>
	</div>

	<div class="mx-6 mt-5 mb-5 w-auto rounded-lg border bg-card p-6 text-left">
		<h2 class="mb-4 text-2xl font-bold">Comments</h2>
		<div class="mb-4 flex items-center gap-2">
			<Input placeholder="Leave a comment..." bind:value={CurrentComment} />
			<Button
				size="icon"
				onclick={() => {
					SubmitModalOpen = true;
				}}><SendIcon /></Button
			>
		</div>
		{#each comments as comment}
			<hr />
			<div class="comment m-4">
				<div class="flex">
					<img
						class="rounded-xlg h-12 w-12 items-center justify-center text-gray-900"
						src="/pfp.png"
						alt="Profile"
					/>
					<div class="column ml-2 flex-col">
						<p class="m-0 p-0 font-bold">{comment.username}</p>
						<p class="m-0 p-0">{comment.text}</p>
						<div class="flex flex-row items-center gap-2">
							<Button
								variant="link"
								class="m-0 p-0 text-red-400"
								onclick={() => {
									ReportedCommentId = comment.id;
									ReportedCommentText = comment.text;
									ReportCommentModalOpen = true;
								}}>Report Comment</Button
							>
							<p class="text-sm text-muted-foreground">
								{timeAgo(comment.created_at)}
							</p>
						</div>
					</div>
				</div>
			</div>
		{/each}
	</div>
	<SubmitCommentModal
		bind:open={SubmitModalOpen}
		company_id={results.id}
		Comment={CurrentComment}
		on:submitted={fetchResults}
	/>
	<ReportCommentModal
		bind:open={ReportCommentModalOpen}
		comment_id={ReportedCommentId!}
		Comment={ReportedCommentText}
		on:submitted={fetchResults}
	/>
	<ReportCompanyModal
		bind:open={ReportProblemModalOpen}
		company_id={results.id!}
		on:submitted={fetchResults}
	/>
{/if}
