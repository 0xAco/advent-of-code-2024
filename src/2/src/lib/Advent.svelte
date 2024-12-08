<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  let lists: number[][] = [];

  function parse(text: string) {
    const txtLists = text.split('\n');
    for (const [i, val] of txtLists.entries()) {
      const lineValues = val.split(/\s/g);
      lists.push(lineValues.map(a => +a));
    };
    lists = lists // update svelte reactive
  }

  onMount(async () => {
    let text = '';
    const response = await fetch('input.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");
    parse(await text);
  });
</script>

{#if lists.length > 0}
  <PartOne lists="{lists}" />
  <PartTwo lists="{lists}" />
{:else}
  <PartOne lists="loading..." />
  <PartTwo lists="loading..." />
{/if}