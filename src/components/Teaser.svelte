<script>
	import Editable from "./Editable.svelte";

	export let teaser;

	let teaserRef;

</script>

<Editable ref={teaserRef}>
	<div class="teaser" data-editable-path={teaser._path} bind:this={teaserRef} >
		<div class="image">
			{#if teaser?.image?._path}
			<img
				src={"https://publish-p54352-e854610.adobeaemcloud.com" + teaser?.image?._path}
				alt={teaser?.imageAltText?.plaintext}
			/>
			{/if}
		</div>
		<div class="textContent">
			<h4>{teaser?.title || ""}</h4>
			<p>{teaser?.description?.plaintext || ""}</p>
			<div class="buttons">
				{#if teaser?.readMoreLink}
					<a href={teaser?.readMoreLink} target="_blank">Read More</a>
				{/if}
				{#if teaser?.sourceLink}
					<a href={teaser?.sourceLink} target="_blank">Source</a>
				{/if}
			</div>
		</div>
	</div>
</Editable>
