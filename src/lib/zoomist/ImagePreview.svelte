<script lang="ts">
	import 'zoomist/css';
	import Zoomist from 'zoomist';
	import type { ZoomistOptions } from 'zoomist/types';
	import { onMount } from 'svelte';

	let zoomistContainer: HTMLDivElement;
	let zoomist: Zoomist;
	let zoomLevel: number;

	let zoomerOptions: ZoomistOptions = {
		slider: false,
		zoomer: false,
		maxScale: 4,
		bounds: true
	};

	onMount(() => {
		zoomist = new Zoomist(zoomistContainer, zoomerOptions);

		zoomist.on('zoom', (_, scale) => {
			zoomLevel = scale;
		});
	});
</script>

<div class="zoomist-container" bind:this={zoomistContainer}>
	<div class="zoomist-wrapper">
		<div class="zoomist-image">
			<slot />
		</div>
	</div>
</div>

<div class="text-white p-4 border rounded-md border-zinc-700 max-w-max mt-4 flex gap-4">
	<span class="text-zinc-400">
		Zoom level : {zoomLevel ? zoomLevel : 1}
	</span>
</div>

<style>
	.zoomist-container {
		width: 100%;
		max-width: 1000px;
	}

	.zoomist-image {
		width: 100%;
		aspect-ratio: 1;
	}
</style>
