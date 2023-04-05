<script>
	// @ts-nocheck

	import "./styles.css";
	import { onMount } from "svelte";
	import Teaser from "../components/Teaser.svelte";
	let data = null;

	let searchParams;
	let topMostEditableElement;

	const handleClick = (event) => {
		const nodeList = document.elementsFromPoint(event.x, event.y);

		topMostEditableElement = nodeList.find(
			(node) => node?.dataset?.editablePath || node.attributes.path
		);
		if (!topMostEditableElement) {
			return;
		}

		const boundingBox = topMostEditableElement.getBoundingClientRect();
		window.parent.postMessage(
			{
				type: "editableBoundingRect",
				payload: [
					boundingBox.top + document.documentElement.scrollTop,
					boundingBox.left,
					boundingBox.height,
					boundingBox.width
				]
			},
			searchParams.get("iFrameHost")
		);

		window.parent.postMessage(
			{
				type: "editablePath",
				payload: [topMostEditableElement?.dataset?.editablePath]
			},
			searchParams.get("iFrameHost")
		);
	};

	const handleResize = () => {
		if (topMostEditableElement) {
			const boundingBox = topMostEditableElement.getBoundingClientRect();
			if (boundingBox) {
				window.parent.postMessage(
					{
						type: "editableBoundingRect",
						payload: [
							boundingBox.top + document.documentElement.scrollTop,
							boundingBox.left,
							boundingBox.height,
							boundingBox.width
						]
					},
					searchParams.get("iFrameHost")
				);
			}
		}
	};

	const handleScroll = () => {
		window.parent.postMessage(
			{
				type: "scrollTop",
				payload: document.documentElement.scrollTop
			},
			searchParams.get("iFrameHost")
		);
	};

	const dataHandler = (event) => {
		if (event.data.type !== "setCfData") {
			return;
		}
		data = event.data.payload.data;
	};

	const editData = (newData) => {
		data = newData
	}

	onMount(async () => {
		const response = await fetch(
			"https://author-p54352-e854610.adobeaemcloud.com/graphql/execute.json/sample-list/Homepage",
			{ credentials: "include" }
		);
		const fetchData = await response.json();

		// using the cfEditorListener, it requires that you set your own data change function specific to the framework you are using.
		window.cfEditorDataFunction = editData

		editData(fetchData?.data?.pageByPath?.item)

		searchParams = new URLSearchParams(window.location.search);
		if (searchParams.get('editMode') === 'HOC') {
			window.addEventListener("message", dataHandler);
			window.addEventListener("click", handleClick);
			window.addEventListener("scroll", handleScroll);
			window.addEventListener("resize", handleResize);
		}

		return () => {
			window.removeEventListener("message", dataHandler);
			window.removeEventListener("click", handleClick);
			window.removeEventListener("scroll", handleScroll);
			window.removeEventListener("resize", handleResize);
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
