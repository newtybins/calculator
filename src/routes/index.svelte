<script lang="ts">
	import * as math from 'mathjs';
	import update from 'immutability-helper';
	import { onMount } from 'svelte';

	let operations: string[] = [];
	let displayElement: HTMLElement;
	let answer = ''; // save the answer for quick input
	const validOperators: string[] = [];

	onMount(() => {
		const operatorElements = document.getElementsByClassName('op');

		for (let i = 0; i < operatorElements.length; i++) {
			const operatorElement = operatorElements[i];
			validOperators.push(operatorElement.textContent);
		}
	})

	const handleClick = (e: Event) => {
		// @ts-ignore this does exist, silly
		const value = e.target.innerText;
		const lastOperation = operations[operations.length - 1];

		switch (value) {
			case 'C':
				operations = [];
				break;

			case '=':
				calculate();
				break;

			case 'ans':
				operations = update(operations, { $push: [answer] });
				break;

			default:
				// Ensure that two of the same operator can not be touching
				console.log(operations);
				if (isNaN(parseInt(value)) && validOperators.includes(lastOperation)) return;
				if (!lastOperation && isNaN(parseInt(value))) return;
				operations = update(operations, { $push: [value] });
				break;
		}
	}

	const fetchExpression = () => operations.join('').replace(/×/g, '*').replace(/÷/g, '/');

	const calculate = () => {
		const expression = fetchExpression();

		try {
			const output = math.evaluate(expression);
			answer = math.format(output, { precision: 14 });
			operations = [answer];
		} catch (e) {
			console.error(e);
			displayElement.style.backgroundColor = '#ff6961';
			setTimeout(() => {displayElement.style.backgroundColor = null}, 1000);
		}
	}

	const onKeyDown = (e: KeyboardEvent) => {
		const code = e.code;
		const lastOperation = operations[operations.length - 1];
		
		if (e.code.startsWith('Digit') && !e.shiftKey) {
			const digit = code.split('Digit')[1];
			operations = update(operations, { $push: [digit] });
		} else if (!isNaN(parseInt(lastOperation))) {
			if (e.shiftKey) {
				switch (e.code) {
					case 'Digit8':
						if (lastOperation === '×') return;
						operations = update(operations, { $push: ['×'] });
						break;

					case 'Equal':
						if (lastOperation === '+') return;
						operations = update(operations, { $push: ['+'] });
						break;
				}
			} else {
				switch (e.code) {
					case 'Equal':
						calculate();
						break;

					case 'Minus':
						if (lastOperation === '-') return;
						operations = update(operations, { $push: ['-'] });
						break;

					case 'Slash':
						if (lastOperation === '÷') return;
						operations = update(operations, { $push: ['÷'] });
						break;
				}
			}
		}
	}
</script>

<svelte:window on:keydown={onKeyDown} />

<div class="grid grid-flow-row auto-rows-max grid-cols-4">
	<div class="bg-black col-span-4 p-10 text-white text-3xl h-28" bind:this={displayElement}>{operations.join('') ?? ''}</div>

	<div class="button" on:click={handleClick}>C</div>
	<div class="button op" on:click={handleClick}>÷</div>
	<div class="button op" on:click={handleClick}>×</div>
	<div class="button op" on:click={handleClick}>-</div>

	<div class="button" on:click={handleClick}>7</div>
	<div class="button" on:click={handleClick}>8</div>
	<div class="button" on:click={handleClick}>9</div>
	<div class="button" on:click={handleClick}>ans</div>

	<div class="button" on:click={handleClick}>4</div>
	<div class="button" on:click={handleClick}>5</div>
	<div class="button" on:click={handleClick}>6</div>
	<div class="button op" on:click={handleClick}>+</div>

	<div class="button" on:click={handleClick}>1</div>
	<div class="button" on:click={handleClick}>2</div>
	<div class="button" on:click={handleClick}>3</div>
	<div class="button" on:click={handleClick}>=</div>
</div>
