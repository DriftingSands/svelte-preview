<script>
	// @ts-nocheck

	import "./styles.css";
	import { onMount } from "svelte";
	import Teaser from "../components/Teaser.svelte";
	import Logo from "../components/Logo.svelte"
	let data = null;

	let searchParams;
	let topMostEditableElement;
	let fetchComplete = false
	let onlyExternalData = false

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
		searchParams = new URLSearchParams(window.location.search);

		// using the cfEditorListener, it requires that you set your own data change function specific to the framework you are using.
		window.cfEditorDataFunction = editData

		if (searchParams.get("onlyExternalData") === "true") {
			onlyExternalData = true
			return
		}

		const response = await fetch(
			"https://author-p54352-e854610.adobeaemcloud.com/graphql/execute.json/sample-list/Homepage",
			{ credentials: "include" }
		);
		fetchComplete = true
		const fetchData = await response.json();

		editData(fetchData?.data?.pageByPath?.item)

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


{#if data?.header || data?.listContent?.length || data?.teaser}
	<main>
		<h1>
			<Logo/>
			{data.header.toUpperCase()}
		</h1>

		<div class="content">
			<ul>
				{#if data?.listContent?.length}
				{#each data.listContent as item}
					<li><p>{item?.plaintext || ""}</p></li>
				{/each}
				{/if}
			</ul>
		</div>
		{#if data.teaser}
			<Teaser teaser={data.teaser} />
		{/if}
	</main>
{:else if onlyExternalData}
	<main>
		<h2>
			<Logo/>
			Waiting for data...
		</h2>
	</main>
{:else if fetchComplete}
	<main>
		<h2>
			<Logo/>
			AEM Fetch failed, log into <a
				target="_blank"
				href="https://author-p54352-e854610.adobeaemcloud.com"
				>https://author-p54352-e854610.adobeaemcloud.com</a
			>
		</h2>
	</main>
{:else}
	<main>
		<h2>
			<Logo/>
			Fetching data...
		</h2>
	</main>
{/if}
