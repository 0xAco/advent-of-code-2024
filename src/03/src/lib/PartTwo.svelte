<script lang="ts">
  const { instructions } = $props();
  let res: number = $state(0);

  if (typeof instructions !== 'string') {
    let shouldDo: boolean = true;
    for (const instruction of instructions) {
      if (instruction === "don't()") {
        shouldDo = false;
        continue;
      }
      if (instruction === 'do()') {
        shouldDo = true;
        continue;
      }

      if(shouldDo) {
        const numbers = instruction.match(/\d+/gm);
        res += numbers[0] * numbers[1];
      }
    }
  }
</script>

<h2>part 2</h2>
<div>instructions using do / don't: {res}</div>