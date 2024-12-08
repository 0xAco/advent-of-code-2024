<script lang="ts">
  const { lists } = $props();
  let res: number = $state(0);

  function compute(l: number[], r: number[]) {
    console.log(lists, l, r);
    for(let i = 0; i < 1000; i++) {
      res += Math.abs(l[i] - r[i]);
    }
  }

  if (typeof lists !== 'string' ) compute(lists.left, lists.right);
</script>

<h2>part 1</h2>
<div>difference: {res}</div>