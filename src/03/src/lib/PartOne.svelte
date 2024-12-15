<script lang="ts">
  const { instructions } = $props();
  let res: number = $state(0);

  if (typeof instructions !== 'string') {
    for (const instruction of instructions) {
      const numbers = instruction.match(/\d+/gm);
      if (!numbers) continue;
      res += numbers[0] * numbers[1];
    }
  }
</script>

<h2>part 1</h2>
<div>instructions added: {res}</div>