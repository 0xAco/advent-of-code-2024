<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

let calibrations = [];

function parse(text: string) {
	const lines = text.split("\n");
	for (const line of lines) {
		const [result, entries] = line.split(":");
		calibrations.push([
			+result,
			entries
				.trim()
				.split(" ")
				.map((a) => +a),
		]);
	}
	// biome-ignore lint/correctness/noSelfAssign: <explanation>
	calibrations = calibrations; // svelte reactive
}

onMount(async () => {
	let text = "";
	const response = await fetch("input.txt");
	// const response = await fetch("example.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	parse(text);
});
</script>

{#if calibrations.length > 0}
  <PartOne calibrations="{calibrations}" />
  <PartTwo calibrations="{calibrations}" />
{:else}
  <PartOne calibrations="loading..." />
  <PartTwo calibrations="loading..." />
{/if}