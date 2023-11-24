<script lang="ts">
	import { page } from '$app/stores';

	let zoom = 13;
	let x = 4093;
	let y = 2726;

	$: {
		zoom = +($page.url.searchParams.get('zoom') ?? 13);
		x = +($page.url.searchParams.get('x') ?? 4093);
		y = +($page.url.searchParams.get('y') ?? 2726);
	}

	let isMouseDown = false;
	let clickX = 0;
	let clickY = 0;
	let offsetX = 0;
	let offsetY = 0;

	let currentOffsetX = 0;
	let currentOffsetY = 0;

	function handleMouseDown(event: PointerEvent) {
		isMouseDown = true;
		clickX = event.clientX;
		clickY = event.clientY;
	}

	function handleMove(event: PointerEvent) {
		if (!isMouseDown) return;

		currentOffsetX = event.clientX - clickX;
		currentOffsetY = event.clientY - clickY;

		if (currentOffsetX > 256) {
			currentOffsetX = 0;
			x = x + 1;
			console.log(x);
		}
	}

	function handleMouseUp() {
		isMouseDown = false;
		offsetX += currentOffsetX;
		offsetY += currentOffsetY;
		currentOffsetX = 0;
		currentOffsetY = 0;
	}
</script>

<svelte:document on:mouseup={handleMouseUp} on:mousemove={handleMove} />

<div class="map" on:mousedown={handleMouseDown}>
	<div
		class="panel"
		style:transform="translate(calc({offsetX + currentOffsetX}px - 50%), calc({offsetY +
			currentOffsetY}px - 50%))"
	>
		<img src="https://tile.openstreetmap.org/{zoom}/{x - 1}/{y - 1}.png" width="256" height="256" />
		<img src="https://tile.openstreetmap.org/{zoom}/{x}/{y - 1}.png" width="256" height="256" />
		<img src="https://tile.openstreetmap.org/{zoom}/{x + 1}/{y - 1}.png" width="256" height="256" />

		<img src="https://tile.openstreetmap.org/{zoom}/{x - 1}/{y}.png" width="256" height="256" />
		<img src="https://tile.openstreetmap.org/{zoom}/{x}/{y}.png" width="256" height="256" />
		<img src="https://tile.openstreetmap.org/{zoom}/{x + 1}/{y}.png" width="256" height="256" />

		<img src="https://tile.openstreetmap.org/{zoom}/{x - 1}/{y + 1}.png" width="256" height="256" />
		<img src="https://tile.openstreetmap.org/{zoom}/{x}/{y + 1}.png" width="256" height="256" />
		<img src="https://tile.openstreetmap.org/{zoom}/{x + 1}/{y + 1}.png" width="256" height="256" />
	</div>

	<a class="top" href="?zoom={zoom}&x={x}&y={y - 1}">&#8593;</a>
	<a class="right" href="?zoom={zoom}&x={x + 1}&y={y}">&#8594;</a>
	<a class="bottom" href="?zoom={zoom}&x={x}&y={y + 1}">&#8595;</a>
	<a class="left" href="?zoom={zoom}&x={x - 1}&y={y}">&#8592;</a>

	<a class="zoom-in" href="?zoom={zoom + 1}&x={x * 2}&y={y * 2}">+</a>
	<a
		class="zoom-out"
		href="?zoom={zoom === 0 ? 0 : zoom - 1}&x={zoom === 0 ? x : Math.trunc(x / 2)}&y={zoom === 0
			? y
			: Math.trunc(y / 2)}">-</a
	>
</div>

<style>
	:global(html, body) {
		width: 100%;
		height: 100%;
		padding: 0;
		margin: 0;
	}

	.map {
		width: 40rem;
		height: 30rem;
		position: relative;
		overflow: hidden;
	}

	.panel {
		display: grid;
		grid-template-columns: 1fr 1fr 1fr;
		width: fit-content;
		position: absolute;
		top: 50%;
		left: 50%;
	}

	.top,
	.right,
	.bottom,
	.left,
	.zoom-in,
	.zoom-out {
		position: fixed;
		background-color: white;
		text-decoration: none;
		display: flex;
		justify-content: center;
		align-items: center;
		width: 1.5rem;
		height: 1.5rem;
		font-size: 1.5rem;
	}

	.top {
		top: 1rem;
		left: 2.5rem;
	}

	.right {
		top: 2.5rem;
		left: 4rem;
	}

	.bottom {
		top: 4rem;
		left: 2.5rem;
	}

	.left {
		top: 2.5rem;
		left: 1rem;
	}

	.zoom-in {
		left: 2.5rem;
		top: 5.5rem;
	}

	.zoom-out {
		left: 2.5rem;
		top: 7rem;
	}

	img,
	.panel {
		pointer-events: none;
		-webkit-user-select: none; /* Safari */
		-ms-user-select: none; /* IE 10 and IE 11 */
		user-select: none; /* Standard syntax */
	}
</style>
