<script lang="ts">
  const { lists } = $props();
  let res: number = $state(0);

  function compute(l: number[], r: number[]) {
    const map = new Map();
    for(const num of r) {
      map.set(num, map.get(num) ? map.get(num) + 1 : 1);
    }
    for(let num of l) {
      res += num * map.get(num)|0;
    }
  }

  if (typeof lists !== 'string' ) compute(lists.left, lists.right);
</script>

<h2>part 2</h2>
<div>similarity: {res}</div>