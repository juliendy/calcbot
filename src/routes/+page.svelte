<script>
	import { onMount } from 'svelte';
	import { evaluate } from 'mathjs';
	import SplitPane from '../components/SplitPlane.svelte';
	import './styles.css';

	let output = '';
	let input = '';
	let modal = false;

	const onInput = (e) => {
		let lastAns = '';
		output = '';
		for (let row of input.split('\n')) {
			try {
				if (['*', '/', '+', '-', '(', '^', '!'].includes(row[0])) {
					row = 'ans' + row;
				}
				row = row.replace(/ans/g, lastAns).replace(/\$/g, '').replace(/,/g, '');
				const result = evaluate(row);
				if (result !== undefined) {
					output += result;
					lastAns = `${result}`;
				}
			} catch (err) {}
			output += '\n';
		}
		output = output.slice(0, -1);
	};

	const onKeyDown = (e) => {
		e.preventDefault();
	};

	const onKeyUp = (e) => {
		if (['Escape', 'Esc'].includes(e.key)) {
			input = '';
			output = '';
		}
	};

	onMount(() => {
		setTimeout(() => {
			document.querySelector('#input')?.focus();
		}, 10);
	});
</script>

<div id="header">
	<button
		id="infoButton"
		on:click={() => {
			modal = !modal;
		}}
		><img id="logo" alt="logo" src="/favicon.png" /> calc.bot
	</button>
</div>
<SplitPane>
	<textarea
		placeholder=""
		slot="left"
		id="input"
		bind:value={input}
		on:input={onInput}
		on:keyup={onKeyUp}
		spellcheck="false"
	/>
	<textarea
		placeholder=""
		slot="right"
		id="output"
		bind:value={output}
		on:keydown={onKeyDown}
		spellcheck="false"
	/>
</SplitPane>
{#if modal}
	<!-- svelte-ignore a11y-click-events-have-key-events -->
	<div
		id="outerModal"
		on:click={(e) => {
			if (e.target.id === 'outerModal') {
				modal = false;
			}
		}}
	>
		<div id="innerModal">
			<p>
				<img id="modalLogo" alt="logo" src="/favicon.png" /> calc.bot made by
				<a target="_blank" rel="noreferrer" href="https://juliendy.dev"
					><img alt="juliendydev" class="thumbnail" src="./image.webp" />juliendydev</a
				>
			</p>
			<p>
				Powered by <a target="_blank" rel="noreferrer" href="https://kit.svelte.dev"
					><img alt="SvelteKit" class="thumbnail" src="./sveltekit.png" />SvelteKit</a
				>
				&
				<a target="_blank" rel="noreferrer" href="https://mathjs.org"
					><img alt="MathJS" class="thumbnail" src="./mathjs.png" />MathJS</a
				>
			</p>
			<p>
				I was inspired by <a
					target="_blank"
					rel="noreferrer"
					href="https://www.skytopia.com/software/opalcalc/"
					><img alt="OpalCalc" class="thumbnail" src="./opalcalc.png" />OpalCalc</a
				> to make a lightweight split-pane calculator. Users can enter expressions in human terms on
				the left and see live results on the right.
			</p>
			<p>
				Press <span>esc</span> to clear, type <span>ans</span> to use the last answer, and convert
				between any units such as <span>hours</span> to <span>minutes</span> or
				<span>centimeters</span>
				to <span>inches</span>.
			</p>
			<p>
				Powered by <a target="_blank" rel="noreferrer" href="https://kit.svelte.dev">SvelteKit</a> &
				<a target="_blank" rel="noreferrer" href="https://www.npmjs.com/package/mathjs">MathJS</a>
			</p>
		</div>
	</div>
{/if}

<style>
	#logo {
		width: 18px;
		height: 18px;
		margin-top: -1px;
		margin-right: 1px;
	}
	#modalLogo {
		width: 18px;
		height: 18px;
		margin-bottom: -4px;
		margin-right: -5px;
	}
	a {
		color: #fff;
	}
	#infoButton {
		all: unset;
		cursor: pointer;
		transition: opacity 0.1s;
		display: flex;
		opacity: 0.5;
	}
	#infoButton:hover {
		opacity: 1;
	}
	#outerModal {
		position: absolute;
		top: 0;
		left: 0;
		height: 100%;
		width: 100%;
		background-color: #66666666;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
	}
	#innerModal {
		width: 350px;
		height: 300px;
		background-color: #111;
		padding: 10px;
		border-radius: 10px;
		font-size: 14px;
		display: flex;
		flex-direction: column;
		text-align: center;
	}

	#innerModal p {
		margin-block: 0.5em;
	}
	#innerModal span {
		color: #ff6600;
	}
	.thumbnail {
		width: 20px;
		height: 20px;
		margin-right: 4px;
		margin-bottom: -4px;
		border-radius: 5px;
	}
	#header {
		position: absolute;
		top: 11px;
		left: 17px;
		font-size: 14px;
		color: #666;
		user-select: none;
	}
	textarea {
		height: calc(100vh - 70px);
	}
	#input {
	}
	#output {
	}
	textarea {
		resize: none;
		outline: none;
		border: 0px solid #ccc;
		background-color: transparent;
		color: #fff;
		font-family: monospace;
		font-size: 24px;
		padding: 30px 20px;
		line-height: 1.5;
		width: calc(100% - 40px);
		height: calc(100vh - 70px);
	}

	#output {
		background-color: #111;
	}
</style>
