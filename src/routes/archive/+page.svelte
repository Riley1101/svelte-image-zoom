<script>
	import ImagePreview from '$lib/scratch/ImagePreview.svelte';
</script>

<main class="p-4 h-screen grid place-items-center">
	<ImagePreview>
		<img
			slot="first"
			src={'/lowres.webp'}
			alt="low resolution rabbit man"
			class="w-full"
			draggable="false"
		/>

		<img
			slot="second"
			src={'/highres.webp'}
			alt="high resolution rabbit man"
			class="w-full"
			draggable="false"
		/>
	</ImagePreview>
</main>
