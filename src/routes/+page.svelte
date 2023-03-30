<!-- <script>
	export let data;
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

{#await data.props.data}
	<p>loading...</p>
{:then data}
	{#each data as item}
		<p>{item.name.first} {item.name.middle} {item.name.last}</p>
	{/each}
{/await} -->

<script>
	import { onMount, onDestroy } from 'svelte';
	let data = null;
	const dataHandler = (event) => {
		console.log('\x1b[31m ~ event:', event);
		if (event.data.type !== 'setCfData') {
			return;
		}
		data = event.data.payload.data;
	};
	onMount(async () => {
		const response = await fetch(
			'https://author-p54352-e854610.adobeaemcloud.com/graphql/execute.json/sample-list/Homepage',
			{ credentials: 'include' }
		);
		const fetchData = await response.json();

		data = fetchData?.data?.pageByPath?.item;
		window.addEventListener('message', dataHandler);

		return () => {
			window.removeEventListener('message', dataHandler);
		};
	});
</script>

{#if data}
	<main>
		<h1>
			<img
				src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Svelte_Logo.svg/1200px-Svelte_Logo.svg.png"
				alt="svelte icon"
				height="24"
			/>
			{data.header.toUpperCase()}
		</h1>

		<div class="content">
			<ul>
				{#each data.pageContent as item}
					<li><p>{item.plaintext}</p></li>
				{/each}
			</ul>
		</div>
	</main>
{:else}
	<main>
		<h2>
			AEM Fetch failed, log into <a
				target="_blank"
				href="https://author-p54352-e854610.adobeaemcloud.com"
				>https://author-p54352-e854610.adobeaemcloud.com</a
			>
		</h2>
	</main>
{/if}

<style>
	* {
		font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
	}

	main {
		display: flex;
		flex-direction: column;
	}

	.content ul {
		list-style: none;
	}
	.content ul li {
		margin-top: 20px;
	}
</style>
