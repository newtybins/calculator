<script lang="ts">
	let display: string = ' ';
	
	const clearDisplay = () => {
		display = '';
	}

	const addDigit = (num: number) => {
		display += num;
	}
</script>

<div class="grid grid-flow-row auto-rows-max grid-cols-4">
	<div class="bg-black col-span-4 p-10 text-white text-3xl h-28">{display}</div>

	<div class="button" on:click={clearDisplay}>C</div>
	<div class="button">÷</div>
	<div class="button">×</div>
	<div class="button">-</div>

	<div class="button" on:click={() => addDigit(7)}>7</div>
	<div class="button" on:click={() => addDigit(8)}>8</div>
	<div class="button" on:click={() => addDigit(9)}>9</div>
	<div class="button disabled"></div>

	<div class="button" on:click={() => addDigit(4)}>4</div>
	<div class="button" on:click={() => addDigit(5)}>5</div>
	<div class="button" on:click={() => addDigit(6)}>6</div>
	<div class="button">+</div>

	<div class="button" on:click={() => addDigit(1)}>1</div>
	<div class="button" on:click={() => addDigit(2)}>2</div>
	<div class="button" on:click={() => addDigit(3)}>3</div>
	<div class="button">=</div>
</div>
