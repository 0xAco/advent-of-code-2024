<script lang="ts">
  const { crossword } = $props();
  let res: number = $state(0);

  interface Coordinates {
    x: number,
    y: number
  }

  function getNE(from: Coordinates) { return crossword[from.x + 1][from.y - 1] };
  function getSO(from: Coordinates) { return crossword[from.x - 1][from.y + 1] };
  function getNO(from: Coordinates) { return crossword[from.x - 1][from.y - 1] };
  function getSE(from: Coordinates) { return crossword[from.x + 1][from.y + 1] };

  function checkMAS(pos: Coordinates) {
    // failure : matrix borders
    if (pos.x <= 0 || pos.x >= crossword[0].length - 1 || pos.y <= 0 || pos.y >= crossword.length - 1)
      return false;
    
    // success
    const ne = getNE(pos);
    const so = getSO(pos);
    const no = getNO(pos);
    const se = getSE(pos);
    if ((ne + 'A' + so === 'SAM' || ne + 'A' + so === 'MAS') && (no + 'A' + se === 'SAM'  || no + 'A' + se === 'MAS'))
      return true;

    return false;
  }

  function compute() {
    // find every 'X' and store their coordinates
    const primers = [];
    for (const x in crossword) {
      for (const y in crossword[x])
        if (crossword[x][y] === 'A') primers.push({x: +x, y: +y});
    }

    // try to form XMAS in every direction
    for (const primer of primers) {
      const found = checkMAS(primer);
      if (found) res++;
    }
  }

  if (typeof crossword !== 'string') {
    compute();
  }
</script>

<h2>part 2</h2>
<div>X shaped MAS: {res}</div>
