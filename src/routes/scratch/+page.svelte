<script>
	import ImagePreview from '$lib/scratch/ImagePreview.svelte';

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
			slot="first"
			src={'./favicon.png'}
			alt="low resolution rabbit man"
			class="w-full"
			draggable="false"
		/>

		<img
			slot="second"
			src={images[1].image}
			alt="high resolution rabbit man"
			class="w-full"
			draggable="false"
		/>
	</ImagePreview>
	<div class="text-zinc-300 text-xl mt-4 flex gap-4">
		<button class="border border-zinc-700 px-4 py-2 rounded-md" on:click={nextImage}>
			{images[currentIndex].name}
		</button>
	</div>
</main>
