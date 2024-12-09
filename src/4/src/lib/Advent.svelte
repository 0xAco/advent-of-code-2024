<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  let crossword: string[][] = [];

  function parse(raw: string) {
    const lines: string[] = raw.split('\n');
    for (const line of lines) {
      crossword.push(line.split(''));
      crossword = crossword; // force svelte update
    }
  }

  onMount(async () => {
    let text='';
    const response = await fetch('input.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");
    parse(await text);
  });
</script>

{#if crossword.length > 0}
  <PartOne crossword="{crossword}" />
  <PartTwo crossword="{crossword}" />
{:else}
  <PartOne crossword="loading..." />
  <PartTwo crossword="loading..." />
{/if}