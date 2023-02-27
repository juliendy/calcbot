<script>
	import { onMount } from 'svelte';
	import { parser } from 'mathjs';
	import { clickOutside } from '../scripts/clickOutside.js';
	import SplitPane from '../components/SplitPlane.svelte';
	import './styles.css';

	let output = '';
	let input = '';
	let modal = '';
	let menu = '';

	const currencyFormatter = new Intl.NumberFormat('de-DE', {
		style: 'currency',
		currency: 'EUR',
		minimumFractionDigits: 2,
		maximumFractionDigits: 2
	});
	const percentageFormatter = new Intl.NumberFormat('de-DE', {
		style: 'percent',
		minimumFractionDigits: 2,
		maximumFractionDigits: 2
	});

	const onInput = (e) => {
		const math = parser();
		let lastAnswer = '';
		output = '';
		let formatDictionary = {};
		for (let row of input.split('\n')) {
			if (['*', '/', '+', '-'].includes(row[0])) {
				row = 'ans' + row;
			}
			let formatAs = '';
			if (
				row.includes('$') ||
				Object.entries(formatDictionary).filter(
					([key, value]) => value === 'currency' && ` ${row} `.includes(key)
				).length
			) {
				formatAs = 'currency';
			} else if (
				row.includes('%') ||
				Object.entries(formatDictionary).filter(
					([key, value]) => value === 'percent' && ` ${row} `.includes(key)
				).length
			) {
				formatAs = 'percent';
			}
			row = row
				.replace(/ans/g, lastAnswer)
				.replace(/ is | are /g, '=')
				.replace(/ and | plus /g, '+')
				.replace(/ minus | without /g, '-')
				.replace(/\$|,|;/g, '');
			let key;
			if (row.includes('=')) {
				const splitRow = row.split('=');
				try {
					math.evaluate(row);
					key = splitRow[0];
				} catch (err) {
					key = splitRow[1];
					if (key) {
						row = `${splitRow[1]} = ${splitRow[0]}`;
					} else {
						row = splitRow[0];
					}
				}
				// @ts-ignore
				formatDictionary[key] = formatAs;
			}
			let result;
			try {
				result = math.evaluate(row);
			} catch (err) {}
			if (result !== undefined && !`${result}`.includes('return fn')) {
				output += `<div class="outputRow">${key ? `<span class="outputTag">${key}</span>` : ''}${
					formatAs === 'currency'
						? currencyFormatter.format(result)
						: formatAs === 'percent'
						? percentageFormatter.format(result)
						: result
				}</div>`;
				lastAnswer = `${result}`;
			} else {
				output += `<div style="height:35px"> </div>`;
			}
			output += '\n';
		}
		output = output.slice(0, -1);
	};

	// const onKeyDown = (e) => {
	// 	e.preventDefault();
	// };

	const onKeyUp = (e) => {
		if (['Escape', 'Esc'].includes(e.key)) {
			input = '';
			output = '';
		}
	};

	onMount(() => {
		setTimeout(() => {
			document.querySelector('#input')?.focus();
		}, 1);
	});
</script>

<div id="header">
	<button
		id="infoButton"
		on:click={() => {
			modal = modal ? '' : 'about';
		}}
		><img id="logo" alt="logo" src="/favicon.png" /> calc.bot
	</button>
	<span class="headerDivider">•</span>
	<div class="toolbarContainer">
		<button
			class="toolbarButton"
			on:click={() => {
				menu = menu ? '' : 'file';
			}}
			on:mouseenter={() => {
				if (menu) menu = 'file';
			}}>File</button
		>
		{#if menu === 'file'}
			<div
				class="menuContainer"
				use:clickOutside
				on:click_outside={() => {
					setTimeout(() => {
						menu = '';
					}, 0);
				}}
			>
				<button
					class="menuButton"
					on:click={async () => {
						menu = '';
						input = '';
						output = '';
					}}>New</button
				>
				<button
					class="menuButton"
					on:click={async () => {
						menu = '';
						let fileHandle;
						[fileHandle] = await window.showOpenFilePicker({
							types: [
								{
									description: 'Calculations',
									accept: {
										'text/calc': ['.calc']
									}
								}
							],
							excludeAcceptAllOption: true,
							multiple: false
						});
						const file = await fileHandle.getFile();
						input = await file.text();
						onInput();
						document.querySelector('#input')?.focus();
					}}>Open</button
				>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Save</button
				>
				<button
					class="menuButton"
					on:click={async () => {
						menu = '';
						const newHandle = await window.showSaveFilePicker({
							types: [
								{
									description: 'Calculation',
									accept: {
										'text/calc': ['.calc']
									}
								}
							]
						});
						const writableStream = await newHandle.createWritable();
						await writableStream.write(input);
						await writableStream.close();
						document.querySelector('#input')?.focus();
					}}>Save as</button
				>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Share</button
				>
			</div>
		{/if}
	</div>
	<div class="toolbarContainer">
		<button
			class="toolbarButton"
			on:click={() => {
				menu = menu ? '' : 'edit';
			}}
			on:mouseenter={() => {
				if (menu) menu = 'edit';
			}}>Edit</button
		>
		{#if menu === 'edit'}
			<div
				class="menuContainer"
				use:clickOutside
				on:click_outside={() => {
					setTimeout(() => {
						menu = '';
					}, 0);
				}}
			>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Cut</button
				>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Copy</button
				>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Paste</button
				>
			</div>
		{/if}
	</div>

	<div class="toolbarContainer">
		<button
			class="toolbarButton"
			on:click={() => {
				menu = menu ? '' : 'view';
			}}
			on:mouseenter={() => {
				if (menu) menu = 'view';
			}}>View</button
		>
		{#if menu === 'view'}
			<div
				class="menuContainer"
				use:clickOutside
				on:click_outside={() => {
					setTimeout(() => {
						menu = '';
					}, 0);
				}}
			>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Switch Theme</button
				>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
					}}>Change Font</button
				>
			</div>
		{/if}
	</div>
	<div class="toolbarContainer">
		<button
			class="toolbarButton"
			on:click={() => {
				menu = menu ? '' : 'help';
			}}
			on:mouseenter={() => {
				if (menu) menu = 'help';
			}}>Help</button
		>
		{#if menu === 'help'}
			<div
				class="menuContainer"
				use:clickOutside
				on:click_outside={() => {
					setTimeout(() => {
						menu = '';
					}, 0);
				}}
			>
				<button
					class="menuButton"
					on:click={() => {
						menu = '';
						modal = 'about';
					}}>About</button
				>
			</div>
		{/if}
	</div>
</div>
<SplitPane>
	<textarea
		slot="left"
		id="input"
		bind:value={input}
		on:input={onInput}
		on:keyup={onKeyUp}
		spellcheck="false"
	/>
	<div slot="right" id="output" spellcheck="false">
		{@html output}
	</div>
</SplitPane>
{#if modal}
	<div id="outerModal">
		<div
			id="innerModal"
			use:clickOutside
			on:click_outside={() => {
				modal = '';
			}}
		>
			{#if modal === 'about'}
				<p>
					<img id="modalLogo" alt="logo" src="./favicon.png" /> calc.bot made by
					<a target="_blank" rel="noreferrer" href="https://juliendy.dev"
						><img alt="juliendy" class="thumbnail" src="./image.webp" />juliendy</a
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
			{/if}
		</div>
	</div>
{/if}

<style>
	.headerDivider {
		color: #666;
	}
	.menuContainer {
		min-width: 90px;
		background-color: #111;
		display: flex;
		flex-direction: column;
	}
	.toolbarContainer {
		display: flex;
		flex-direction: column;
	}
	.toolbarButton,
	.menuButton {
		all: unset;
		margin: 5px;
		opacity: 0.5;
		transition: opacity 0.01s;
	}
	.toolbarButton {
		margin-top: 0px;
	}
	.menuButton {
		margin: 5px 10px;
	}
	.toolbarButton:hover,
	.menuButton:hover {
		opacity: 1;
	}
	.menuContainer {
		display: flex;
		flex-direction: column;
		position: absolute;
		top: 20px;
	}
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
		height: 10px;
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
		user-select: none;
		width: 100%;
		display: flex;
		flex-direction: row;
		gap: 10px;
		width: calc(100% - 20px);
	}
	textarea {
		height: calc(100vh - 70px);
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
		height: calc(100vh - 28px);
		padding-top: 28px;
		padding-left: 20px;
	}
</style>
