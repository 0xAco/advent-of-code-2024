<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  let text = '';

  onMount(async () => {
    const response = await fetch('input.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");
  });
</script>

{#if text.length > 0}
  <PartOne text="{text}" />
  <PartTwo text="{text}" />
{:else}
  <PartOne text="loading..." />
  <PartTwo text="loading..." />
{/if}