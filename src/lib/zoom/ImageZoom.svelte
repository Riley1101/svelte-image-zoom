<script lang="ts">
	import 'zoomist/css';
	import Zoomist from 'zoomist';
	import type { ZoomistOptions } from 'zoomist/types';
	import { onMount } from 'svelte';

	let zoomistContainer: HTMLDivElement;
	let indicator: HTMLDivElement;
	let previewImage: HTMLDivElement;

	let zoomerOptions: ZoomistOptions = {
		slider: false,
		zoomer: false,
		maxScale: 10,
		bounds: true
	};

	function onMouseMove(e: MouseEvent) {
		let { clientX } = e;
		let boundingRect = previewImage.getBoundingClientRect();
		let mousePosition = clientX - boundingRect.left;
		let percent = mousePosition / boundingRect.width;
		let clip = 100 - percent * 100;
		previewImage.style.setProperty('--width', clip.toString() + '%');
		indicator.style.setProperty('--left', `${percent * 100}%`);
	}

	onMount(() => {
		new Zoomist(zoomistContainer, zoomerOptions);
	});
</script>

<div on:mousemove={onMouseMove} role="button" tabindex="0" class="rounded-md overflow-hidden">
	<div class="zoomist-container" bind:this={zoomistContainer}>
		<div class="zoomist-wrapper">
			<div class="zoomist-image relative">
				<div>
					<slot name="first" />
				</div>
				<div bind:this={previewImage} class="previewImage absolute top-0 left-0 w-full h-full">
					<slot name="second" />
				</div>
				<div
					bind:this={indicator}
					class="w-[3px] h-full bg-gray-400/80 absolute top-0 left-0 z-100 indicator tralsate-x-[-100%] flex items-center"
				></div>
			</div>
		</div>
	</div>
</div>

<style>
	.zoomist-container {
		width: 100%;
		max-width: 600px;
	}

	.zoomist-image {
		width: 100%;
		aspect-ratio: 1;
	}

	.previewImage {
		--width: 0%;
		clip-path: inset(0 var(--width) 0 0);
	}

	.indicator {
		--left: 0px;
		left: var(--left);
	}
</style>
