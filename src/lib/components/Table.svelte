<script lang="ts">
	import DataTable, { Head, Body, Row, Cell, Pagination } from '@smui/data-table';
	import LinearProgress from '@smui/linear-progress';
	import Button from '@smui/button';
	import Select, { Option } from '@smui/select';
	import IconButton from '@smui/icon-button';
	import { Label } from '@smui/common';

	export let items: any[] = [];
	export let loaded = false;
	let rowsPerPage = 10;
	let currentPage = 0;

	$: start = currentPage * rowsPerPage;
	$: end = Math.min(start + rowsPerPage, items.length);
	$: slice = items.slice(start, end);
	$: lastPage = Math.max(Math.ceil(items.length / rowsPerPage) - 1, 0);

	$: if (currentPage > lastPage) {
		currentPage = lastPage;
	}

	async function deletePost(id: number) {
		const res = await fetch(`https://api.fake-rest.refine.dev/posts/${id}`, {
			method: 'DELETE'
		}).then((res) => {
			res.json();
			location.reload();
		});
	}
</script>

<DataTable table$aria-label="User list" style="width: 100%;">
	<Head>
		<Row>
			<Cell numeric>ID</Cell>
			<Cell>Title</Cell>
			<Cell>Image</Cell>
			<Cell>Date Created</Cell>
			<Cell>Actions</Cell>
		</Row>
	</Head>
	<Body>
		{#each slice as item (item.id)}
			<Row>
				<Cell numeric>{item.id}</Cell>
				<Cell>{item.title}</Cell>
				<Cell
					><img width="100" src={`https://picsum.photos/100/100?random=${item.id}`} alt="" /></Cell
				>
				<Cell>{item.createdAt}</Cell>
				<Cell>
					<a href={`/post/${item.id}`}>Edit</a>
					<Button on:click={() => deletePost(item.id)}>Delete</Button>
				</Cell>
			</Row>
		{/each}
	</Body>
	<Pagination slot="paginate">
		<svelte:fragment slot="rowsPerPage">
			<Label>Rows Per Page</Label>
			<Select variant="outlined" bind:value={rowsPerPage} noLabel>
				<Option value={10}>10</Option>
				<Option value={25}>25</Option>
				<Option value={100}>100</Option>
			</Select>
		</svelte:fragment>
		<svelte:fragment slot="total">
			{start + 1}-{end} of {items.length}
		</svelte:fragment>

		<IconButton
			class="material-icons"
			action="first-page"
			title="First page"
			on:click={() => (currentPage = 0)}
			disabled={currentPage === 0}>first_page</IconButton
		>
		<IconButton
			class="material-icons"
			action="prev-page"
			title="Prev page"
			on:click={() => currentPage--}
			disabled={currentPage === 0}>chevron_left</IconButton
		>
		<IconButton
			class="material-icons"
			action="next-page"
			title="Next page"
			on:click={() => currentPage++}
			disabled={currentPage === lastPage}>chevron_right</IconButton
		>
		<IconButton
			class="material-icons"
			action="last-page"
			title="Last page"
			on:click={() => (currentPage = lastPage)}
			disabled={currentPage === lastPage}>last_page</IconButton
		>
	</Pagination>

	<LinearProgress
		indeterminate
		bind:closed={loaded}
		aria-label="Data is being loaded..."
		slot="progress"
	/>
</DataTable>
