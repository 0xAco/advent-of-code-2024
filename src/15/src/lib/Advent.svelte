<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

interface Day15 {
	map: string[][];
	instructions: [];
}
let inputs: Day15 = {
	map: [],
	instructions: [],
};

function parse(text) {
	const split = text.split("\n\n");
	console.log(split);
}

onMount(async () => {
	let text = "";
	const response = await fetch("input.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	parse(text);

	// biome-ignore lint/correctness/noSelfAssign: <explanation>
	inputs = inputs; // svelte reactive
});
</script>

{#if inputs.map.length > 0}
  <PartOne inputs="{inputs}" />
  <PartTwo inputs="{inputs}" />
{:else}
  <PartOne inputs="loading..." />
  <PartTwo inputs="loading..." />
{/if}