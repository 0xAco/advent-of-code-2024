<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  let instructions: string[] = [];

  function parse(text: string) {
    instructions = [...text.match(/do\(\)|don't\(\)|mul\(\d+,\d+\)/gm)];
  }

  onMount(async () => {
    let text='';
    const response = await fetch('input.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");
    parse(await text);
  });
</script>

{#if instructions.length > 0}
  <PartOne instructions="{instructions}" />
  <PartTwo instructions="{instructions}" />
{:else}
  <PartOne instructions="loading..." />
  <PartTwo instructions="loading..." />
{/if}