<script>
	import ImageLoupe from '$lib/loupe/ImageLoupe.svelte';
	// click to zoom
</script>

<main class="p-4 bg-zinc-800 h-screen grid place-items-center">
	<ImageLoupe>
		<img
			src={'./lowres.webp'}
			alt="low resolution rabbit man"
			class="w-full max-"
			draggable="false"
		/>
	</ImageLoupe>
</main>
