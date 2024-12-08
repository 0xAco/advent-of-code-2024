<script lang="ts">
  const { lists } = $props();
  let res: number = $state(0);

  function isSafe (list: number[]) {
    let sorting: string | null = null;
    let prev: number = -1;

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

  function isSafeDampener(list: number[]) {
    // test the full array
    if (isSafe(list)) return true;

    // try rebuilding array, removing num or prev
    for (let i = 0; i < list.length ; i++) {
      if (isSafe(list.toSpliced(i, 1))) return true;
    }

    return false;
  }

  if (typeof lists !== 'string') {
    for (const list of lists)
      if (isSafeDampener(list)) res ++;
  }
</script>

<h2>part 2</h2>
<div>safe lists with dampener: {res}</div>
