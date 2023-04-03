<script>
	// @ts-nocheck

	export let teaser;
</script>

<div class="teaser" data-editable-path={teaser?._path}>
	<div class="image">
		<img
			src={"https://author-p54352-e854610.adobeaemcloud.com" + teaser?.image._path}
			alt={teaser?.imageAltText.plaintext}
		/>
	</div>
	<div class="textContent">
		<h4>{teaser?.title}</h4>
		<p>{teaser?.description?.plaintext}</p>
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
