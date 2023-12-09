<script>
	import ImageZoom from '$lib/zoom/ImageZoom.svelte';
</script>

<div class="p-4 justify-center grid pt-24">
	<ImageZoom>
		<img
			src={'/lowres.webp'}
			alt="low resolution rabbit man"
			class="w-full"
			draggable="false"
			slot="first"
		/>
		<img
			src={'/highres.webp'}
			alt="high resolution rabbit man"
			class="w-full"
			draggable="false"
			slot="second"
		/>
	</ImageZoom>
</div>
