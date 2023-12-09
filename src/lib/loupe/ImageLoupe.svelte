<script lang="ts">
	export let showStats = false;

	let img: HTMLImageElement, imgBounds: DOMRect;
	let comparismImage: HTMLDivElement;
	let glass: HTMLDivElement;

	let MIN_ZOOM = 0.5;
	let MAX_ZOOM = 3;

	let glassWidth = 400;
	let glassHeight = 400;

	let relativeX = 0;
	let relativeY = 0;

	let glassoffsetX = 0;
	let glassoffsetY = 0;

	let showZoom = true;
	let zoomFactor = 0.6;

	let isComparing = false;

	let mgMouseOffsetX = 0,
		mgMouseOffsetY = 0;

	function moveRegularImage() {
		glass.style.setProperty(
			'--left',
			`calc(${relativeX * 100}% - ${glassWidth / 2}px + ${glassoffsetX}px - 4px)`
		);
		glass.style.setProperty(
			'--top',
			`calc(${relativeY * 100}% - ${glassHeight / 2}px + ${glassoffsetY}px - 4px)`
		);
		glass.style.setProperty(
			'background-position',
			`calc(${relativeX * 100}% + ${glassWidth / 2}px - ${relativeX * 400}px) 
             calc(${relativeY * 100}% + ${glassHeight / 2}px - ${relativeY * 400}px)`
		);

		glass.style.setProperty(
			'background-size',
			`${zoomFactor * imgBounds.width}% ${zoomFactor * imgBounds.height}%`
		);
	}

	function moveComparismImage() {
		if (!comparismImage) return;

		comparismImage.style.setProperty(
			'--left',
			`calc(${relativeX * 100}% - ${glassWidth / 2}px + ${glassoffsetX}px - 4px)`
		);

		comparismImage.style.setProperty(
			'--top',
			`calc(${relativeY * 100}% - ${glassHeight / 2}px + ${glassoffsetY}px - 4px)`
		);

		comparismImage.style.setProperty(
			'background-position',
			`calc(${relativeX * 100}% + ${glassWidth / 2}px - ${relativeX * 400}px) calc(${
				relativeY * 100
			}% + ${400 / 2}px - ${relativeY * 400}px)`
		);

		comparismImage.style.setProperty(
			'background-size',
			`${zoomFactor * imgBounds.width}% ${zoomFactor * imgBounds.height}%`
		);
	}

	function onTouchMove(e: TouchEvent) {
		if (!imgBounds) return;
		const target = e.target as HTMLElement;

		let _relX = (e.touches[0].clientX - imgBounds.left) / target.clientWidth;
		let _relY = (e.touches[0].clientY - imgBounds.left) / target.clientHeight;

		if (_relX >= 0 && _relY >= 0 && _relX <= 1 && _relY <= 1) {
			glassoffsetY = mgMouseOffsetX;
			glassoffsetY = mgMouseOffsetY;
			relativeX = _relX;
			relativeY = _relY;
			showZoom = true;
		} else {
			showZoom = false;
		}
	}

	function calculateImageBounds() {
		if (!img) return;
		imgBounds = img.getBoundingClientRect();
	}

	function onMouseWheel(e: WheelEvent) {
		if (!imgBounds) return;

		let direction = e.deltaY < 0 ? 1 : -1;

		if (direction === 1) {
			if (zoomFactor >= MAX_ZOOM) return;
			zoomFactor += 0.1;
		} else {
			if (zoomFactor <= MIN_ZOOM) return;
			zoomFactor -= 0.1;
		}

		glass.style.setProperty(
			'background-size',
			`${zoomFactor * imgBounds.width}% ${zoomFactor * imgBounds.height}%`
		);
	}

	function onMouseMove(e: MouseEvent) {
		if (!imgBounds) return;

		const target = e.target as HTMLElement;

		relativeX = (e.clientX - imgBounds.left) / target.clientWidth;
		relativeY = (e.clientY - imgBounds.top) / target.clientHeight;

		glassoffsetX = mgMouseOffsetX;
		glassoffsetY = mgMouseOffsetY;

		moveRegularImage();
		moveComparismImage();

		showZoom = true;
	}

	function onMouseDown() {
		isComparing = true;
		moveComparismImage();
	}

	function onMouseUp() {
		isComparing = false;
	}
</script>

<div class="overflow-hidden relative border border-gray-200 max-w-max rounded-md w-full">
	<div
		role="button"
		tabindex="0"
		on:mousedown={onMouseDown}
		on:mouseup={onMouseUp}
		aria-label="Zoomable image with Loupe"
	>
		<img
			on:blur={() => {}}
			on:load={calculateImageBounds}
			on:mouseenter={calculateImageBounds}
			on:touchstart|preventDefault={calculateImageBounds}
			on:mousemove={onMouseMove}
			on:touchmove={onTouchMove}
			on:mouseout={() => (showZoom = false)}
			on:touchend={() => (showZoom = false)}
			src={'/highres.webp'}
			alt="High resolution business monkey "
			bind:this={img}
			draggable="false"
			class="aspect-square w-full md:max-w-[600px]"
			on:wheel={onMouseWheel}
		/>
	</div>

	{#if imgBounds}
		<div
			class:opacity-0={!showZoom}
			bind:this={glass}
			class="magnifying_glass absolute z-12 rounded-full border border-gray-400 overflow-hidden pointer-events-none grid"
			aria-label="Magnifying glass that shows zoomed in image"
		>
			<div
				bind:this={comparismImage}
				class="w-full h-full comparism_image"
				class:hidden={!isComparing}
			></div>
		</div>
	{/if}
</div>

{#if showStats}
	<div class="mt-4 text-gray-800 border border-gray-200 max-w-max p-4 rounded-md" role="status">
		<span class="text-sm text-gray-700"> Zoom : </span>
		<progress
			max={MAX_ZOOM}
			value={zoomFactor}
			class="rounded-md appearance-none h-[10px]
                [&::-webkit-progress-bar]:rounded-lg
                [&::-webkit-progress-value]:rounded-lg
                [&::-webkit-progress-bar]:bg-slate-300
                [&::-webkit-progress-value]:bg-red-500
                [&::-moz-progress-bar]:bg-violet-400"
		>
			{Math.round(zoomFactor * 100) / 100}
		</progress>

		<br />

		<span class="text-gray-700 text-sm">
			X : {Math.round(relativeX * 100)}%
		</span>
		/
		<span class="text-gray-700 text-sm">
			Y : {Math.round(relativeY * 100)}%
		</span>
	</div>
{/if}

<style>
	.comparism_image {
		--background-image: url('/lowres.webp');
		--width: 400px;
		--height: 400px;
		--left: 0%;
		--top: 0%;
		--clip-width: 0px;
		width: var(--width);
		height: var(--height);
		left: var(--left);
		top: var(--top);
		background-image: var(--background-image);
	}
	.magnifying_glass {
		--background-image: url('/highres.webp');
		--left: 0%;
		--top: 0%;
		--width: 400px;
		--height: 400px;
		width: var(--width);
		height: var(--height);
		will-change: auto;
		left: var(--left);
		top: var(--top);
		background-image: var(--background-image);
	}
</style>
