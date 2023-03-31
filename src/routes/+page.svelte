<script>
	// @ts-nocheck

	import './styles.css';
	import { onMount } from 'svelte';
	import Teaser from '../components/Teaser.svelte';
	let data = null;

	const dataHandler = (event) => {
		if (event.data.type !== 'setCfData') {
			return;
		}
		console.log("\x1b[31m ~ data:", data)
		data = event.data.payload.data;
		console.log("\x1b[31m ~ data:", data)
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
				{#each data.listContent as item}
					<li><p>{item.plaintext}</p></li>
				{/each}
			</ul>
		</div>
		{#if data.teaser}
			<Teaser teaser={data.teaser} />
		{/if}
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

