<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  type LabMap = string[][];
  let labMap: LabMap = [];

  onMount(async () => {
    let text='';
    const response = await fetch('input.txt');
    // const response = await fetch('example.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");

    labMap = text.split('\n').map(row => row.split(''));
  });
</script>

{#if labMap.length > 0}
  <PartOne labMap="{labMap}" />
  <PartTwo labMap="{labMap}" />
{:else}
  <PartOne labMap="loading..." />
  <PartTwo labMap="loading..." />
{/if}