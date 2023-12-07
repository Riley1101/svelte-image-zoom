<script lang="ts">
	let img: HTMLImageElement, imgBounds: DOMRect;
	let glass: HTMLDivElement;

	let relativeX = 0;
	let relativeY = 0;
	let glassoffsetX = 0;
	let glassoffsetY = 0;
	let showZoom = true;
	let zoomFactor = 0.6;

	let mgMouseOffsetX = 0,
		mgMouseOffsetY = 0;

	function onMouseMove(e: MouseEvent) {
		if (!imgBounds) return;
		const target = e.target as HTMLElement;
		relativeX = (e.clientX - imgBounds.left) / target.clientWidth;
		relativeY = (e.clientY - imgBounds.top) / target.clientHeight;
		glassoffsetX = mgMouseOffsetX;
		glassoffsetY = mgMouseOffsetY;
		glass.style.setProperty(
			'--left',
			`calc(${relativeX * 100}% - ${400 / 2}px + ${glassoffsetX}px - 4px)`
		);
		glass.style.setProperty(
			'--top',
			`calc(${relativeY * 100}% - ${400 / 2}px + ${glassoffsetY}px - 4px)`
		);

		glass.style.setProperty(
			'background-position',
			`calc(${relativeX * 100}% + ${400 / 2}px - ${relativeX * 400}px) calc(${relativeY * 100}% + ${
				400 / 2
			}px - ${relativeY * 400}px)`
		);

		glass.style.setProperty(
			'background-size',
			`${zoomFactor * imgBounds.width}% ${zoomFactor * imgBounds.height}%`
		);

		showZoom = true;
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
</script>

<div class="overflow-hidden relative">
	<img
		on:blur={() => {}}
		on:load={calculateImageBounds}
		on:mouseenter={calculateImageBounds}
		on:touchstart|preventDefault={calculateImageBounds}
		on:mousemove={onMouseMove}
		on:touchmove={onTouchMove}
		on:mouseout={() => (showZoom = false)}
		on:touchend={() => (showZoom = false)}
		src="./lowres.webp"
		alt="low res business monkey absolute"
		bind:this={img}
	/>

	{#if imgBounds}
		<div
			class:opacity-0={!showZoom}
			bind:this={glass}
			class="magnifying_glass absolute z-12 border-4 transition-opacity pointer-events-none"
		/>
	{/if}
</div>

<style>
	.magnifying_glass {
		--background-image: url('./lowres.webp');
		--left: 0%;
		--top: 0%;
		width: 400px;
		height: 400px;
		left: var(--left);
		top: var(--top);
		background-image: var(--background-image);
	}
</style>
