<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

let topmap: number[][] = [];

onMount(async () => {
	let text = "";
	const response = await fetch("input.txt");
	// const response = await fetch("example.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	topmap = text.split("\n").map((line) => line.split("").map((a) => +a));
});
</script>

{#if topmap.length > 0}
  <PartOne topmap="{topmap}" />
  <PartTwo topmap="{topmap}" />
{:else}
  <PartOne topmap="loading..." />
  <PartTwo topmap="loading..." />
{/if}