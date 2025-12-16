<script lang="ts">
	import * as Empty from '$lib/components/ui/empty/index.js';
	import Header from '$lib/components/ui/xenon/Header.svelte';

	import { Button } from '$lib/components/ui/button/index.js';
	import { SearchX } from '@lucide/svelte';

	import { page } from '$app/stores';

	import Search from '$lib/components/ui/xenon/Search.svelte';

	$: slug = $page.params.slug;

	import { goto } from '$app/navigation';
	import { toast } from 'svelte-sonner';
	import { Input } from '$lib/components/ui/input/index.js';
	import * as Command from '$lib/components/ui/command/index.js';
	let query = '';
	let results: any[] = [];
	let loading = false;
	let active = false;

	async function load() {
		loading = true;

		const res = await fetch(
			`https://evribackend.between-my-legs-there-is-a.monster/companies?internal_name=ilike.**`
		);

		results = await res.json();
		loading = false;
	}
</script>

<Header />

<Search classes="w-full p-3" relclasses="w-full" />

<div class="flex h-full items-center justify-center">
	<Empty.Root>
		<Empty.Header>
			<Empty.Media variant="icon">
				<SearchX />
			</Empty.Media>
			<Empty.Title>No Results</Empty.Title>
			<Empty.Description>
				We couldn't find any companies matching "{slug}"
			</Empty.Description>
		</Empty.Header>
	</Empty.Root>
</div>
