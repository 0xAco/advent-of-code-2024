<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  const lists: number[]|never = [];

  onMount(async () => {
    let text='';
    const response = await fetch('input.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");
  });
</script>

{#if lists.length > 0}
  <PartOne lists="{lists}" />
  <PartTwo lists="{lists}" />
{:else}
  <PartOne lists="loading..." />
  <PartTwo lists="loading..." />
{/if}