<script lang="ts">
  import PartOne from './PartOne.svelte';
  import PartTwo from './PartTwo.svelte';
  import { onMount } from 'svelte';

  interface Input {
    rules: Map<number, Set<number>> | null,
    updates: number[][] | null,
  }
  let input: Input = { rules: null, updates: null };

  function parse(text: string) {
    const moses: string[] = text.split('\n\n');
    const rulesText: string[] = moses[0].split('\n');
    const updatesText: string[] = moses[1].split('\n');
    
    const rules: Map<number, Set<number>> = new Map();
    for (const rule of rulesText) {
      const [valText, keyText] = rule.split('|');
      if (keyText.length && valText.length) {
        if (rules.has(+keyText))
          rules.set(+keyText, rules.get(+keyText).add(+valText));
        else
          rules.set(+keyText, new Set([+valText]));
      }
    }

    input.rules = rules;
    input.updates = updatesText.map(update => update.split(',').map(page => +page));
  }

  onMount(async () => {
    let text='';
    const response = await fetch('input.txt');
    // const response = await fetch('example.txt');
    if (response.ok) text = await response.text();
    else console.error("failed to load the file.");

    parse(text);
  });
</script>

{#if input.rules && input.updates}
  <PartOne input="{input}" />
  <PartTwo input="{input}" />
{:else}
  <PartOne input="loading..." />
  <PartTwo input="loading..." />
{/if}