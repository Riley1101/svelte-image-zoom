<script>
	import ImagePreview from '$lib/zoomist/ImagePreview.svelte';

	let images = [
		{
			id: 1,
			image: '/lowres.webp',
			name: 'Low Res'
		},
		{
			id: 2,
			image: '/highres.webp',
			name: 'High Res'
		}
	];

	$: currentIndex = 0;
	function nextImage() {
		currentIndex++;
		if (currentIndex >= images.length) currentIndex = 0;
	}
</script>

<main class="p-4 bg-zinc-800 h-screen grid place-items-center">
	<ImagePreview>
		<img
			src={images[0].image}
			alt="low resolution rabbit man"
			class="w-full"
			draggable="false"
			slot="first"
		/>
		<img
			src={images[1].image}
			alt="high resolution rabbit man"
			class="w-full"
			draggable="false"
			slot="second"
		/>
	</ImagePreview>
</main>
