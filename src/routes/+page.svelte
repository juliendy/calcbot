<script>
	import { onMount } from 'svelte';
	import './styles.css';
	import { evaluate } from 'mathjs';
	import SplitPane from '../components/SplitPlane.svelte'
	let output = '';
	let input = '';
	let modal = false;
	const onInput = () => {
		let lastAns = '';
		output = '';
		for (let row of input.split('\n')) {
			try {
				if (['*', '/', '+', '-', '(', '^', '!'].includes(row[0])) {
					row = 'ans' + row;
				}
				row = row.replace('ans', lastAns).replace(/\$/g, '').replace(/,/g, '');
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
	onMount(() => {
		setTimeout(() => {
			document.querySelector('#input')?.focus();
		}, 100);
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
		placeholder="Start typing an expression"
		slot="left"
		id="input"
		bind:value={input}
		on:input={onInput}
	/>
	<textarea
		placeholder="See the results here"
		slot="right"
		id="output"
		bind:value={output}
		on:keydown={onKeyDown}
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
				<a target="_blank" rel="noreferrer" href="https://juliendy.dev">juliendy</a>
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
		margin-right: 2px;
	}
	#modalLogo {
		width: 18px;
		height: 18px;
		margin-top: 2px;
	}
	a {
		color: #fff;
	}
	#infoButton {
		all: unset;
		cursor: pointer;
		transition: color 0.1s;
		display: flex;
	}
	#infoButton:hover {
		color: #fff;
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
		width: 300px;
		height: 90px;
		background-color: #111;
		padding: 10px;
		border-radius: 10px;
		font-size: 14px;
		display: flex;
		flex-direction: column;
		text-align: center;
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
	}
</style>