<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  interface Lists {
    left: number[],
    right: number[]
  }

  let lists: Lists = { left: [], right: [] };

  function parse(txt: string) {
    const l: number[] = [];
    const r: number[] = [];
    const numbers: string[] = txt.split(/\s+/gm);
    for (const [i, val] of numbers.entries()) {
      if (i % 2 === 0) l.push(+val);
      else r.push(+val);
    }
    l.sort((a, b) => a - b);
    r.sort((a, b) => a - b);
    lists.left = l;
    lists.right = r;
  }

  onMount(async () => {
    let text = '';
    const response = await fetch('input.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");
    parse(await text);
  });
</script>

{#if lists.left.length + lists.right.length > 0 }
  <PartOne lists="{lists}" />
  <PartTwo lists="{lists}" />
{:else}
  <PartOne lists="loading..." />
  <PartTwo lists="loading..." />
{/if}