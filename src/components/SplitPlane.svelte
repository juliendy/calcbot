<script>
	export let leftInitialSize = '66%';
	let left;
	let isDragging = false;
	function dragstart() {
		isDragging = true;
	}
	function drag(e) {
		if (!isDragging) return;
		const elementLeft = left.getBoundingClientRect().left;
		left.style.flexBasis = e.clientX - elementLeft + 'px';
	}
	function dragend() {
		if (!isDragging) return;
		isDragging = false;
	}
</script>

<div class="split-pane" on:mousemove={drag} on:mouseup={dragend}>
	<div bind:this={left} class="left" style="flex-basis: {leftInitialSize}">
		<slot name="left" />
	</div>
	<div class="splitter" on:mousedown={dragstart} />
	<div class="right">
		<slot name="right" />
	</div>
</div>

<style>
	.splitter {
		flex-grow: 0;
		flex-shrink: 0;
		width: 3px;
		background-color: #666;
		cursor: col-resize;
		transition: background-color 0.1s;
	}
	.splitter:hover {
		background-color: #fff;
	}
	.split-pane {
		display: flex;
		align-items: stretch;
		width: 100%;
		max-width: 100%;
	}
	.split-pane > div {
		display: block;
	}
	.left {
		flex-grow: 0;
		flex-shrink: 0;
	}
	.right {
		flex-grow: 1;
		flex-shrink: 1;
		overflow-x: auto;
	}
</style>
