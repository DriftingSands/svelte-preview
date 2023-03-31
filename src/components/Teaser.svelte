<script>
  export let teaser;
  const {image, imageAltText, title, description, readMoreLink, sourceLink} = teaser
</script>

<div class="teaser">
  <div class="image">
    <img
      src={'https://author-p54352-e854610.adobeaemcloud.com' + image._path}
      alt={imageAltText.plaintext}
    />
  </div>
  <div class="textContent">
    <h4>{title}</h4>
    <p>{description?.plaintext}</p>
    <div class="buttons">
      {#if readMoreLink}
      <a href={readMoreLink} target="_blank">Read More</a>
      {/if}
      {#if sourceLink}
      <a href={sourceLink} target="_blank">Source</a>
      {/if}
    </div>
  </div>
</div>