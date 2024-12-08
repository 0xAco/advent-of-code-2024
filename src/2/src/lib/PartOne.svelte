<script lang="ts">
  const { lists } = $props();
  let res: number = $state(0);

  function isSafe(list: number[]) {
    let sorting: string | null = null;
    let prev = -1;

    for (const [i, num] of list.entries()) {
      if (i > 0) {
        // check if variation x is in [1;3]
        if (Math.abs(prev - num) < 1 || Math.abs(prev - num) > 3) return false;
        // define sorting if needed
        if (!sorting) sorting = (prev < num) ? 'asc' : 'desc';
        // check if sorting is ok
        else {
          if (sorting === 'asc' && prev > num || sorting === 'desc' && prev < num)
            return false;
        }
      }

      prev = num;
    }

    return true;
  }

  if (typeof lists !== 'string') {
    for (const list of lists)
      if (isSafe(list)) res ++;
  }
</script>

<h2>part 1</h2>
<div>safe lists: {res}</div>
