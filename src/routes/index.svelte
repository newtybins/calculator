<script lang="ts">
	import * as math from 'mathjs'; // todo: own math parser
	import update from 'immutability-helper';
	import { onMount } from 'svelte';

	let operations: string[] = [];
	let displayElement: HTMLElement;
	let answer: string = null;
	const validOperators: string[] = [];

	onMount(() => {
		const operatorElements = document.getElementsByClassName('op');

		for (let i = 0; i < operatorElements.length; i++) {
			const operatorElement = operatorElements[i];
			validOperators.push(operatorElement.textContent);
		}
	});

	const handleClick = (e: Event) => {
		// @ts-ignore this does exist, silly
		const { innerText: operation } = e.target;

		switch (operation) {
			case 'C':
				clear();
				break;

			case '=':
				if (operations.length === 0) return;
				calculate();
				break;

			case 'ans':
				if (!answer) return;
				operations = update(operations, { $push: [answer] });
				break;

			default:
				if (!shouldPlaceOperator(operation)) return;
				operations = update(operations, { $push: [operation] });
				break;
		}
	};

	const clear = () => {
		operations = update(operations, { $splice: [[0, operations.length]] });
	};

	const shouldPlaceOperator = (operator: string) => {
		const last = operations?.[operations.length - 1]?.trim();
		const beforeLast = operations?.[operations.length - 2]?.trim();

		return !(operator !== '-' // If the operator is not a minus...
			? isNaN(parseInt(operator)) && isNaN(parseInt(last)) && last !== '!' // ..ensure that operators never touch
			: operations.length === 1 // If there is only one operator...
			? last === '-' // ensure only one minus can be there
			: isNaN(parseInt(beforeLast)) // If the operation before last is an operator...
			? last === '-' // ensure only one minus can be there, once again
			: last === '-' && beforeLast === '-'); // ...otherwise, ensure that only two minuses can touch
	};

	const sanitiseExpression = () =>
		operations.join('').replace(/×/g, '*').replace(/÷/g, '/').replace(/ /g, '');

	const calculate = () => {
		const expression = sanitiseExpression();

		try {
			const output = math.evaluate(expression);
			answer = math.format(output, { precision: 14 });
			operations = [answer];
		} catch (e) {
			console.error(`There was an error with calculating your result. Reason: ${e.message}`);
			displayElement.style.backgroundColor = '#ff6961';
			setTimeout(() => {
				displayElement.style.backgroundColor = null;
			}, 1000);
		}
	};

	const onKeyDown = (e: KeyboardEvent) => {
		const { code } = e;
		const last = operations?.[operations.length - 1]?.trim();

		if (e.code.startsWith('Digit') && !e.shiftKey) {
			// Do not allow digits after the factorial sign
			if (last === '!') return;

			// Clear the operations if the answer is the last operation
			if (last === answer) clear();

			// If there is a minus sign before this number and one before that sign, remove the space after the number
			if (last === '-' && isNaN(parseInt(operations?.[operations.length - 2])))
				operations = update(operations, {
					[operations.length - 1]: {
						$set: ' -'
					}
				});

			const digit = code.split('Digit')[1];
			operations = update(operations, { $push: [digit] });
		} else {
			if (e.shiftKey) {
				let operation: string = null;
				let keyPressed = true;

				switch (code) {
					// *
					case 'Digit8':
						operation = '×';
						break;

					// +
					case 'Equal':
						operation = '+';
						break;

					// !
					case 'Digit1':
						operation = '!';
						break;

					default:
						keyPressed = false;
						break;
				}

				// Factorial specific rules
				if (operation === '!' && isNaN(parseInt(last))) return;

				if (operation !== '!') {
					if (!shouldPlaceOperator(operation) || !keyPressed) return;
					operation = ` ${operation} `;
				}

				operations = update(operations, { $push: [operation] });
			} else if (e.ctrlKey) {
				switch (code) {
					// CTRL + V
					case 'KeyV':
						if (!answer) return;
						operations = update(operations, { $push: [answer] });
						break;
				}
			} else {
				switch (code) {
					// ENTER or =
					case 'Enter':
					case 'Equal':
						if (operations.length === 0) return;
						calculate();
						break;

					// -
					case 'Minus':
						if (!shouldPlaceOperator('-')) return;
						operations = update(operations, { $push: [' - '] });
						break;

					// /
					case 'Slash':
						if (!shouldPlaceOperator('÷')) return;
						operations = update(operations, { $push: [' ÷ '] });
						break;

					// BACKSPACE
					case 'Backspace':
						console.log(operations);
						operations = update(operations, { $splice: [[-1, 1]] });
						break;
				}
			}
		}
	};
</script>

<svelte:window on:keydown={onKeyDown} />

<!-- Add a button for factorials -->
<div class="grid grid-flow-row auto-rows-max grid-cols-4">
	<div class="bg-black col-span-4 p-10 text-white text-3xl h-28 display" bind:this={displayElement}>
		{operations.join('') ?? ''}
	</div>

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
