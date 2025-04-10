<script lang="ts">
	let waterConsumed = $state(0);
	let dailyGoal = $state(2000); // 2 liters (2000ml) daily goal
	let lastDrink: Date | null = $state(null);
	let firstRun = $state(true);
	function addWater(amount: number) {
		waterConsumed += amount;
	}

	function resetWater() {
		waterConsumed = 0;
		lastDrink = null;
	}

	const blocksToFill = $derived(Math.min(Math.floor(waterConsumed / 100), 20));
	const waterBlocks = $derived(
		Array(20)
			.fill(false)
			.map((_, index) => index < blocksToFill)
	); // 20 blocks representing 100ml each
	const lastDrinkDerived = $derived(waterConsumed > 0 ? new Date() : null);

	$effect(() => {
		// Persist data to localStorage whenever values change
		if (!firstRun) {
			localStorage.setItem(
				'waterData',
				JSON.stringify({
					waterConsumedSaved: waterConsumed,
					lastDrinkSaved: lastDrink,
					dailyGoalSaved: dailyGoal
				})
			);
		} else {
			const waterData = localStorage.getItem('waterData');
			if (waterData) {
				const { waterConsumedSaved, lastDrinkSaved, dailyGoalSaved } = JSON.parse(waterData);
				waterConsumed = waterConsumedSaved;
				lastDrink = lastDrinkSaved ? new Date(lastDrinkSaved) : null;
				dailyGoal = dailyGoalSaved;
			}
			firstRun = false;
		}
	});
</script>

<div class="flex flex-col items-center justify-center h-screen bg-gray-100">
	<div class="flex flex-col items-center justify-center gap-2 my-10">
		<h1 class="text-4xl font-bold">Drink Your Water</h1>
		<p class="text-lg">A simple app to help you drink more water</p>
	</div>

	<div class="flex flex-row gap-4">
		<button
			onclick={() => addWater(250)}
			class="bg-blue-500 text-white p-2 rounded-md hover:bg-blue-600 cursor-pointer">250ml</button
		>
		<button
			onclick={() => addWater(500)}
			class="bg-blue-500 text-white p-2 rounded-md hover:bg-blue-600 cursor-pointer">500ml</button
		>
		<button
			onclick={() => addWater(750)}
			class="bg-blue-500 text-white p-2 rounded-md hover:bg-blue-600 cursor-pointer">750ml</button
		>
		<button
			onclick={() => addWater(1000)}
			class="bg-blue-500 text-white p-2 rounded-md hover:bg-blue-600 cursor-pointer">1000ml</button
		>
	</div>

	<div class="flex flex-row gap-4 mt-4">
		<button
			class="bg-red-500 text-white p-2 rounded-md hover:bg-red-600 cursor-pointer"
			onclick={resetWater}>Reset</button
		>
	</div>

	<div class="mt-4 flex flex-col items-center">
		<h2 class="text-xl font-semibold mb-2">Daily Water Progress</h2>
		<p class="mb-4">{waterConsumed}ml / {dailyGoal}ml</p>

		<div class="grid grid-cols-5 gap-2 mb-6">
			{#each waterBlocks as filled, i}
				<div class={`w-12 h-12 rounded-md border ${filled ? 'bg-blue-500' : 'bg-gray-200'}`}></div>
			{/each}
		</div>
	</div>
	<div class="flex flex-col items-center justify-center gap-2">
		<h2>Time of last drink</h2>
		<p>{lastDrinkDerived ? lastDrinkDerived.toLocaleTimeString() : 'No drinks yet'}</p>
	</div>
</div>
