<script>
	import { onMount, onDestroy } from "svelte";
	export let ref;

	if (new URLSearchParams(window.location.search).get("editMode") === "HOC") {
		function scrollTo(event) {
			if (event?.data?.type !== "scrollToPath" || event.data.path !== ref.dataset.editablePath) {
				return;
			}
			const box = ref.getBoundingClientRect();
			if (box.top <= 0 && box.bottom >= window.innerHeight) {
				return;
			}
			window.scrollBy({ top: box.top, left: 0, behavior: "smooth" });
		}

		onMount(() => window.addEventListener("message", scrollTo));
		onDestroy(() => window.removeEventListener("message", scrollTo));
	}
</script>

<slot />
