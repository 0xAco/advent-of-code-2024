<script lang="ts">
  const { input } = $props();
  let res: number = $state(0);

  function checkUpdate(update: number[]): boolean {
    // filter dependencies to only include pages in this update
    const filteredGraph: Map<number, Set<number>> = new Map([...input.rules]);
    // filter pages and pages dependencies
    for (const page of filteredGraph.keys()) {
      if (!update.includes(page)) filteredGraph.delete(page);
      else {
        const updateSet = new Set(update);
        const intersection = new Set([...filteredGraph.get(page)].filter(page => updateSet.has(page)));
        if (intersection.size) filteredGraph.set(page, intersection);
        else filteredGraph.delete(page);
      }
    }

    for (const [i, page] of update.entries()) {
      const pageDeps: Set<number> = filteredGraph.get(page);
      if (!pageDeps) continue;
      else {
        // check every page dependency is already printed
        for (const pageDep of pageDeps) {
          if (!update.slice(0, i).includes(pageDep)) return false;
        }
      }
    }

    return true;
  }

  if (typeof input !== 'string') {
    for (const update of input.updates) {
      if (checkUpdate(update)) {
        res += update[Math.floor(update.length/2)];
      };
    }
  }
</script>

<h2>part 1</h2>
<div>good updates middle pages sum: {res}</div>
