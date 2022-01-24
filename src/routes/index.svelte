<script lang="ts">
	import * as math from 'mathjs';
	import update from 'immutability-helper';

	let operations: string[] = [];
	let displayElement: HTMLElement;
	let answer = ''; // save the answer for quick input

	const handleClick = (e: Event) => {
		// @ts-ignore this does exist, silly
		const value = e.target.innerText;

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
				if (isNaN(value) && operations[operations.length - 1] === value) return;
				operations = update(operations, { $push: [value] });
				break;
		}
	}

	const calculate = () => {
		const expression = operations.join('');

		try {
			const output = math.evaluate(expression);
			answer = math.format(output, { precision: 14 });
			operations = [answer];
		} catch (e) {
			displayElement.style.backgroundColor = '#ff6961';
			setTimeout(() => {displayElement.style.backgroundColor = null}, 1000);
		}
	}
</script>

<div class="grid grid-flow-row auto-rows-max grid-cols-4">
	<div class="bg-black col-span-4 p-10 text-white text-3xl h-28" bind:this={displayElement}>{operations.join(' ') ?? ''}</div>

	<div class="button" on:click={handleClick}>C</div>
	<div class="button" on:click={handleClick}>÷</div>
	<div class="button" on:click={handleClick}>×</div>
	<div class="button" on:click={handleClick}>-</div>

	<div class="button" on:click={handleClick}>7</div>
	<div class="button" on:click={handleClick}>8</div>
	<div class="button" on:click={handleClick}>9</div>
	<div class="button" on:click={handleClick}>ans</div>

	<div class="button" on:click={handleClick}>4</div>
	<div class="button" on:click={handleClick}>5</div>
	<div class="button" on:click={handleClick}>6</div>
	<div class="button" on:click={handleClick}>+</div>

	<div class="button" on:click={handleClick}>1</div>
	<div class="button" on:click={handleClick}>2</div>
	<div class="button" on:click={handleClick}>3</div>
	<div class="button" on:click={handleClick}>=</div>
</div>
