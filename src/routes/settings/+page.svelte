<script lang="ts">
	import { invoke } from '@tauri-apps/api/tauri';
	import { fade } from 'svelte/transition';
	import { configuration } from '../../stores';
	import type { Config } from '../../stores';

	let success: boolean;
	let prov: string;
	let keys: string;

	async function onSubmit(): Promise<void> {
		event?.preventDefault();
		await invoke<Config>('set_config', { provider: prov, keystore: keys })
			.then((message) => {
				$configuration = {
					provider: message.provider,
					keystore: message.keystore
				};
				success = true;
				setTimeout(() => (success = false), 6000);
			})
			.catch((error) => console.error(error));
	}
</script>

<div class="navbar rounded-xl place-content-center mt-5">
	<a href="./" class="btn btn-ghost normal-case text-xl">Home</a>
	<a href="/deploy" class="btn btn-ghost normal-case text-xl">Deploy</a>
	<a href="/keystore" class="btn btn-ghost normal-case text-xl">Create Keystore</a>
</div>
{#if success === true}
	<div transition:fade class="alert alert-success">
		<svg
			xmlns="http://www.w3.org/2000/svg"
			class="stroke-current shrink-0 h-6 w-6"
			fill="none"
			viewBox="0 0 24 24"
			><path
				stroke-linecap="round"
				stroke-linejoin="round"
				stroke-width="2"
				d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
			/></svg
		>
		<span>Settings Saved Successfully!</span>
	</div>
{/if}
<div class="flex flex-col justify-center items-center">
	<h1 class="text-3xl font-bold text-emerald-700 mt-10">Set your config!</h1>
</div>
<div class="flex flex-col justify-center items-center h-screen min-h-screen">
	<form on:submit={() => onSubmit()}>
		<div class="form-control w-full max-w-xs mb-5">
			<!-- svelte-ignore a11y-label-has-associated-control -->
			<label class="label">
				<span class="label-text">Keystore Path</span>
			</label>
			<input
				type="text"
				bind:value={keys}
				placeholder="|> path"
				class="input input-bordered w-full max-w-xs"
				required
			/>
		</div>
		<div class="form-control w-full max-w-xs mb-5">
			<!-- svelte-ignore a11y-label-has-associated-control -->
			<label class="label">
				<span class="label-text">JSON-RPC Provider</span>
			</label>
			<input
				type="text"
				bind:value={prov}
				placeholder="|> url"
				class="input input-bordered w-full max-w-xs"
				required
			/>
		</div>
		<div class="flex flex-col justify-center items-center">
			<button type="submit" class="btn btn-primary rounded-xl border-8 mt-5">Save </button>
		</div>
	</form>
</div>
