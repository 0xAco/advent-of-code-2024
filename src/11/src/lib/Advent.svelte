<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

let list: string[] = [];

onMount(async () => {
	let text = "";
	// const response = await fetch("example.txt");
	const response = await fetch("input.txt");
	// const response = await fetch("debug.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	list = text.split(" ");
});
</script>

{#if list.length > 0}
  <PartOne list="{list}" />
  <PartTwo list="{list}" />
{:else}
  <PartOne list="loading..." />
  <PartTwo list="loading..." />
{/if}