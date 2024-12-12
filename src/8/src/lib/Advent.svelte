<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

let grid: string[][] = [];

onMount(async () => {
	let text = "";
	const response = await fetch("input.txt");
	// const response = await fetch("example.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	grid = text.split("\n").map((line) => line.split(""));
});
</script>

{#if grid.length > 0}
  <PartOne grid="{grid}" />
  <PartTwo grid="{grid}" />
{:else}
  <PartOne grid="loading..." />
  <PartTwo grid="loading..." />
{/if}