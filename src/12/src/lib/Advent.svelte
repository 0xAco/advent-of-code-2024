<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

let garden: string[][] = [];

onMount(async () => {
	let text = "";
	// const response = await fetch("example.txt");
	const response = await fetch("input.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	garden = text.split("\n").map((line) => line.split(""));
});
</script>

{#if garden.length > 0}
  <PartOne garden="{garden}" />
  <PartTwo garden="{garden}" />
{:else}
  <PartOne garden="loading..." />
  <PartTwo garden="loading..." />
{/if}