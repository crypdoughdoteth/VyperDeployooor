<script lang="ts">
	import { invoke } from '@tauri-apps/api/tauri';
	import { onMount } from 'svelte';

	type DeploymentData = {
		sc_name: string;
		deployer_address: string;
		deploy_date: string;
		sc_address: string;
		network: string;
	};

	onMount(async () => {
		await getData();
		totalPosts = allContent!.length;
		totalPages = Math.ceil(totalPosts / postsPerPage);
		getItems();
		loading = false;
	});

	let allContent: Array<DeploymentData> | undefined;
	let currentPage = 1;
	const postsPerPage = 6;
	let totalPosts: number;
	let totalPages: number;
	let loading = true;
	let curr_items: DeploymentData[];
	//console.log(curr_items);
	function getItems(): DeploymentData[] {
		const startIndex = (currentPage - 1) * postsPerPage;
		const endIndex = startIndex + postsPerPage;
		const slice = allContent!.slice(startIndex, endIndex);
		curr_items = slice;
		curr_items = curr_items;
		return curr_items;
	}

	const setCurrentPage = (newPage: number) => {
		currentPage = newPage;
		getItems();
	};

	async function getData(): Promise<void> {
		// trigger DB dump of deployment details on Rust side
		await invoke<Array<DeploymentData>>('db_read', {})
			.then((message) => {
				allContent = message;
			})
			.catch((err) => {
				console.error(err);
			});
	}
</script>

<div class="navbar rounded-xl place-content-center mt-5">
	<a href="./" class="btn btn-ghost normal-case text-xl">Home</a>
	<a href="/deploy" class="btn btn-ghost normal-case text-xl">Deploy</a>
	<a href="/settings" class="btn btn-ghost normal-case text-xl">Settings</a>
</div>
<div class="flex flex-col justify-center items-center">
	<h1 class="text-3xl font-bold text-emerald-700 mt-10">Deployed Contracts</h1>
</div>
<div class="flex flex-col justify-center items-center h-screen min-h-screen">
	<div>
		<table class="table">
			<!-- head -->
			<thead>
				<tr>
					<th>Name</th>
					<!-- <th>Deployer Address</th> -->
					<th>Date</th>
					<th>Contract Address</th>
					<th>Network</th>
				</tr>
			</thead>
			<tbody class="flex-1 flex-col justify-center items-center">
				{#if loading === true}
					<tr>
						<h5 class="text-center mb-5 mt-5">Loading...</h5>
					</tr>
				{:else if loading === false && curr_items.length === 0}
					<tr>
						<h5 class="mb-5 mt-5 self-center">Nothing to display</h5>
					</tr>
				{:else}
					{#each curr_items as row}
						<tr class="hover">
							<td>{row.sc_name}</td>
							<!-- <td>{row.deployer_address}</td> -->
							<td>{row.deploy_date}</td>
							<td>{row.sc_address}</td>
							<td>{row.network}</td>
						</tr>
					{/each}
				{/if}
			</tbody>
		</table>
		<div class="flex flex-col join items-center mt-10">
			<div>
				{#if currentPage === 1}
					<button
						on:click|preventDefault={() => setCurrentPage(currentPage - 1)}
						class="join-item btn btn-next-prev"
						disabled>«</button
					>
				{:else}
					<button
						on:click|preventDefault={() => setCurrentPage(currentPage - 1)}
						class="join-item btn btn-next-prev">«</button
					>
				{/if}
				{#if currentPage === totalPages}
					<button
						on:click|preventDefault={() => setCurrentPage(currentPage + 1)}
						class="join-item btn btn-next-prev"
						disabled>»</button
					>
				{:else}
					<button
						on:click|preventDefault={() => setCurrentPage(currentPage + 1)}
						class="join-item btn btn-next-prev">»</button
					>
				{/if}
			</div>
		</div>
	</div>
</div>
