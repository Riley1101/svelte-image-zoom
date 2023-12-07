<script lang="ts">
	type State = {
		currentZoom: number;
		currentTranslateX: number;
		currentTranslateY: number;
		availableTranslateX: number;
		availableTranslateY: number;
	};

	const MAX_W = 1000;
	const MAX_H = 1000;

	const MIN_ZOOM = 1;
	const MAX_ZOOM = 1000;
	const ZOOM_SPEED = 0.1;

	let previewImage: HTMLDivElement;
	let imageContainer: HTMLDivElement;
	let indicator: HTMLDivElement;
	let isPanning = false;

	let imageState: State;
	$: imageState = {
		currentZoom: 1,
		currentTranslateX: 0,
		currentTranslateY: 0,
		availableTranslateX: 0,
		availableTranslateY: 0
	};

	function onZoom(e: WheelEvent) {
		e.preventDefault();
		let direction = e.deltaY < 0 ? 1 : -1;
		let newZoom = imageState.currentZoom + ZOOM_SPEED * direction;
		if (newZoom < MIN_ZOOM || newZoom > MAX_ZOOM) return;

		imageState.currentZoom = Math.round(newZoom * 100) / 100;

		// Reset translation when zooming out to the minimum zoom level
		if (imageState.currentZoom === MIN_ZOOM) {
			imageState.currentTranslateX = 0;
			imageState.currentTranslateY = 0;
		}

		imageState = imageState;
		sync_available_translate();
		sync_image_container();
	}

	let mousePosition = 0;
	function onMouseMove(e: MouseEvent) {
		let { movementX, movementY, clientX } = e;
		// for image compare
		let boundingRect = imageContainer.getBoundingClientRect();
		mousePosition = clientX - boundingRect.left;

		let percent = mousePosition / boundingRect.width;
		indicator.style.setProperty('--left', `${percent * 100}%`);

		// math mouse position with zoom
		mousePosition = mousePosition * imageState.currentZoom;

		previewImage.style.setProperty('--width', mousePosition.toString() + 'px');

		// for panning
		if (!isPanning) return;

		let newTranslateX = imageState.currentTranslateX + movementX;
		let newTranslateY = imageState.currentTranslateY + movementY;

		imageState.currentTranslateX = newTranslateX;
		imageState.currentTranslateY = newTranslateY;

		imageState = imageState;
		sync_available_translate();
		sync_image_container();
	}

	function sync_image_container() {
		imageContainer.style.setProperty('--scale', imageState.currentZoom.toString());
		imageContainer.style.setProperty('--translate-x', `${imageState.currentTranslateX}px`);
		imageContainer.style.setProperty('--translate-y', `${imageState.currentTranslateY}px`);
	}

	function onMouseLeave() {
		isPanning = false;
	}

	function sync_available_translate() {
		let newAvailableX = (MAX_W * imageState.currentZoom - MAX_W) / 2;
		let newAvailableY = (MAX_H * imageState.currentZoom - MAX_H) / 2;

		imageState.availableTranslateX = newAvailableX;
		imageState.availableTranslateY = newAvailableY;

		imageState = imageState;
	}
</script>

<div
	class="border-2 relative w-full p-0 overflow-hidden max-w-max border-zinc-700 rounded-md cursor-pointer"
>
	<div
		role="button"
		tabindex="0"
		aria-label="Image preview"
		class="image-container relative aspect-square w-[1000px] select-none"
		bind:this={imageContainer}
		on:mouseleave={onMouseLeave}
		on:wheel={onZoom}
		on:mousedown={() => {
			isPanning = true;
		}}
		on:mousemove={onMouseMove}
		on:mouseup={() => {
			isPanning = false;
		}}
	>
		<div class="first abolute top-0 left-0 bg-gray-200">
			<slot name="first" />
		</div>
		<div class="second absolute top-0 left-0 bg-green-200" bind:this={previewImage}>
			<slot name="second" />
		</div>
		<div
			class="indicator w-[5px] h-full border bg-red-200 absolute top-0 left-0"
			bind:this={indicator}
		></div>
	</div>
</div>
<div class="text-white p-4 border rounded-md border-zinc-700 max-w-max mt-4 flex gap-4">
	<span class="text-zinc-400">
		is panning: {isPanning}
	</span>

	<span class="text-zinc-400">
		Current zoom: {imageState.currentZoom}
	</span>
	<span class="text-zinc-400">
		TranslateX: {imageState.currentTranslateX}
		/ {imageState.availableTranslateX}
	</span>
	<span class="text-zinc-400">
		TranslateY: {imageState.currentTranslateY}
		/ {imageState.availableTranslateY}
	</span>
</div>

<style>
	.indicator {
		--left: 0px;
		left: var(--left);
	}

	.image-container {
		--scale: 1;
		--translate-x: 0px;
		--translate-y: 0px;
		transform: translate(var(--translate-x, 0px), var(--translate-y, 0px)) scale(var(--scale, 0));
		transition-property: scale;
		transition-duration: 0s;
		transition-delay: 0s;
	}
	.second {
		--width: 1000px;
		clip: rect(0px, var(--width), 1000px, 0px);
	}
</style>
