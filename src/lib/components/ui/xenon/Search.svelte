<script lang="ts">
	export let classes = '';
	export let relclasses = '';
	import { goto } from '$app/navigation';
	import { Search } from '@lucide/svelte';
	import { toast } from 'svelte-sonner';
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';
	import * as Command from '$lib/components/ui/command/index.js';
	let query = '';
	let results: any[] = [];
	let loading = false;
	let active = false;

	let timeout: ReturnType<typeof setTimeout>;
	$: {
		clearTimeout(timeout);

		timeout = setTimeout(async () => {
			loading = true;

			const res = await fetch(
				`https://evribackend.between-my-legs-there-is-a.monster/companies?internal_name=ilike.**`
			);

			results = await res.json();
			loading = false;
		}, 250);
	}

	function gotoSearch() {
		console.log('search', query.length);
		if (query.length == 0) {
			toast.error('Error!', {
				description: 'Enter a search term.'
			});
		}
		goto(`/search/${query}`);
	}
</script>

<div class="flex items-center gap-1 {classes} ">
	<div class="relative rounded-md border-1 {relclasses}">
		<Command.Root>
			<Command.Input
				placeholder="Do they use Evri..?"
				onfocus={() => (active = true)}
				onblur={() => (active = false)}
				bind:value={query}
			/>
			{#if active}
				<Command.List class="absolute top-full right-0 left-0 z-50 rounded-md bg-card shadow ">
					{#if loading}
						<Command.Loading>Searching...</Command.Loading>
					{/if}

					<Command.Empty>No results found.</Command.Empty>

					<Command.Group heading="Results" class="text-left">
						{#each results as result}
							<Command.Item onSelect={() => goto(`/company/${result.internal_name}`)}>
								<div
									class="flex h-full w-full {result.uses_evri
										? 'text-red-300'
										: 'text-green-300'} cursor-pointer justify-between"
									role="button"
									tabindex="0"
									on:mousedown|preventDefault
								>
									<span>{result.display_name}</span>
									<p class="text-xs text-muted-foreground">
										{result.uses_evri ? 'Evri ' : 'NOT Evri'}
									</p>
								</div>
							</Command.Item>
						{/each}
					</Command.Group>

					<Command.Separator />
				</Command.List>
			{/if}
		</Command.Root>
	</div>

	<!--<Button variant="secondary" size="icon" onclick={gotoSearch}><Search /></Button>-->
</div>
