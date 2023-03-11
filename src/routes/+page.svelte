<script lang="ts">
	import { onMount } from 'svelte';
	import Button from '@smui/button';
	import Dialog from '$lib/components/Dialog.svelte';
	import Table from '$lib/components/Table.svelte';

	type Post = {
		createdAt: Date;
		image: any;
		content: string;
		title: string;
		id: number;
	};

	async function loadThings() {
		const res = await fetch(`https://api.fake-rest.refine.dev/posts`);
		const data = await res.json();
		if (res.ok) {
			return data;
		} else {
			throw new Error(data);
		}
	}

	let open = false;
</script>

<div style="display:flex; flex-direction:column; justify-content:center; align-items:center;">
	<div style="display:flex; justify-content:space-between;">
		<Button on:click={() => (open = true)}>Add New</Button>
	</div>
	{#await loadThings()}
		<Table loaded={false} />
	{:then data}
		<Table items={data} loaded={true} />
	{:catch error}
		alguma coisa deu errado {error.message}
	{/await}
</div>

<Dialog {open} />
