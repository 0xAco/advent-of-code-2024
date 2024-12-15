<script lang="ts">
  const { crossword } = $props();
  let res: number = $state(0);

  interface Coordinates {
    x: number,
    y: number
  }

  type Direction = 'N' | 'NE' | 'E' | 'SE' | 'S' | 'SO' | 'O' | 'NO';

  const XMAS = new Map();
  XMAS.set('X', 'M');
  XMAS.set('M', 'A');
  XMAS.set('A', 'S');
  XMAS.set('S', 'EOS');

  function buildXMAS(pos: Coordinates | null, direction: Direction, expected: string) {
    // success
    if (expected === 'EOS')
      return true;
    // failure : index out of bounds or null
    if (pos === null || pos.x < 0 || pos.y < 0 || pos.x > crossword[0].length - 1 || pos.y > crossword.length - 1)
      return false;
    // failure : unexpected letter
    if (crossword[pos.x][pos.y] !==  expected)
      return false;

    // find next cell to check
    let next: Coordinates | null = null;
    switch (direction) {
      case 'N':
        next = {x: pos.x, y: pos.y - 1};
        break;
      case 'NE':
        next = {x: pos.x + 1, y: pos.y - 1};
        break;
      case 'E':  
        next = {x: pos.x + 1, y: pos.y};
        break;
      case 'SE':
        next = {x: pos.x + 1, y: pos.y + 1};
        break;
      case 'S':
        next = {x: pos.x, y: pos.y + 1};
        break;
      case 'SO':
        next = {x: pos.x - 1, y: pos.y + 1};
        break;
      case 'O':
        next = {x: pos.x - 1, y: pos.y};
        break;
      case 'NO':
        next = {x: pos.x - 1, y: pos.y - 1};
        break;
    }

    return buildXMAS(next, direction, XMAS.get(expected));
  }

  function compute() {
    // find every 'X' and store their coordinates
    const primers = [];
    for (const x in crossword) {
      for (const y in crossword[x])
        if (crossword[x][y] === 'X') primers.push({x: +x, y: +y});
    }

    // try to form XMAS in every direction
    const allDirections: Direction[] = ['N', 'NE', 'E', 'SE', 'S', 'SO', 'O', 'NO'];
    for (const primer of primers) {
      for (const direction of allDirections) {
        const found = buildXMAS(primer, direction, 'X');
        if (found) res++;
      }
    }
  }

  if (typeof crossword !== 'string') {
    compute();
  }
</script>

<h2>part 1</h2>
<div>XMAS: {res}</div>
