<script lang="ts">
  const { input } = $props();
  let res: number = $state(0);

  type Rules = Map<number, Set<number>>;
  type Update = number[];

  // filter dependencies to only include pages in this update
  function filterRules(rules: Rules, update: Update) {
    const filteredRules: Rules = new Map([...rules]);
    // filter pages and pages dependencies
    for (const page of filteredRules.keys()) {
      if (!update.includes(page)) filteredRules.delete(page);
      else {
        const updateSet = new Set(update);
        const intersection = new Set([...filteredRules.get(page)].filter(page => updateSet.has(page)));
        if (intersection.size) filteredRules.set(page, intersection);
        else filteredRules.delete(page);
      }
    }
    return filteredRules;
  }

  function checkUpdate(update: Update): boolean {
    const filteredRules = filterRules(input.rules, update);

    for (const [i, page] of update.entries()) {
      const pageDeps: Set<number> = filteredRules.get(page);
      if (!pageDeps) continue;
      else {
        // check that every page dependency is already printed
        for (const pageDep of pageDeps) {
          if (!update.slice(0, i).includes(pageDep)) return false;
        }
      }
    }

    return true;
  }

  function reorder(wrong: Update): Update {
    const filteredRules: Rules = filterRules(input.rules, wrong);
    const right: Update = [];
    const remaining: Update = [...wrong];

    console.log(input.rules);
    console.log(wrong);

    // 0 rules -> add everything
    for (const page of wrong) {
      if (!filteredRules.has(page)) {
        right.push(page);
        remaining.splice(remaining.indexOf(page), 1);
      }
    }

    while (right.length < wrong.length) {
      let loop = 1;
      const timeout = 1000;
      for (let i = 1; i < wrong.length; i++) {
        for (const [page, deps] of filteredRules.entries()) {
          if (i === deps.size) {
            right.push(page);
            remaining.splice(remaining.indexOf(page), 1);
            filteredRules.delete(page);
          }
        }
      }
      if (loop >= timeout) {
        console.error('timeout reached');
        break;
      }
      else loop++;
    }

    return right;
  }

  if (typeof input !== 'string') {
    for (const update of input.updates) {
      if (!checkUpdate(update)) {
        console.group();
        const reorderedUpdate = reorder(update);
        res += reorderedUpdate[Math.floor(reorderedUpdate.length/2)];
        console.log(reorderedUpdate, reorderedUpdate[Math.floor(reorderedUpdate.length/2)]);
        console.groupEnd();
      };
    }
  }
</script>

<h2>part 1</h2>
<div>rearranged wrong updates middle pages sum: {res}</div>