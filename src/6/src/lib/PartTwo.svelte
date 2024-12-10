<script lang="ts">
  const { labMap } = $props();
  let res: number = $state(0);

  type Position = {
    x: number,
    y: number
  };
  type Direction = 'N' | 'E' | 'S' | 'W';
  const directions: Map<Direction, Direction> = new Map();
  directions.set('N', 'E');
  directions.set('E', 'S');
  directions.set('S', 'W');
  directions.set('W', 'N');

  // find the guard start pos
  function findGuard(): Position {
    for (let i = 0; i < labMap[0].length; i++)
      for (let j = 0; j < labMap.length; j++)
        if (labMap[i][j] === '^') return { x: i, y: j }
  }

  function getNextCell(pos: Position, dir: Direction): Position {
    switch (dir) {
      case 'N':
        return { x: pos.x - 1, y: pos.y };
      case 'E':
        return { x: pos.x, y: pos.y + 1};
      case 'S':
        return { x: pos.x + 1, y: pos.y };
      case 'W':
        return { x: pos.x, y: pos.y - 1 };
    }
  }

  function rotateRight(dir: Direction) {
    return directions.get(dir);
  }

  function computeGuard(): Set<string> {
    const visited = new Set<string>;
    let guardPos: Position = findGuard();
    let direction: Direction = 'N';

    visited.add('x' + guardPos.x + 'y' + guardPos.y);

    let next: Position = null;
    do {
      next = getNextCell(guardPos, direction);
      if (labMap[next.x][next.y] === '#') {
        direction = rotateRight(direction);
      } else {
        visited.add('x' + guardPos.x + 'y' + guardPos.y);

        // update map for visuals
        labMap[guardPos.x][guardPos.y] = 'X';
        
        // update for next loop
        guardPos.x = next.x
        guardPos.y = next.y;

        labMap[guardPos.x][guardPos.y] = 'o';

        /*
        console.group();
        for (const row of labMap) console.log(row);
        console.groupEnd();
        */
      }
    } while(getNextCell(guardPos, direction).x >= 0 && getNextCell(guardPos, direction).x < labMap.length && getNextCell(guardPos, direction).y >= 0 && getNextCell(guardPos, direction).y < labMap[0].length);

    visited.add('x' + guardPos.x + 'y' + guardPos.y);
    return visited;
  }

  if (typeof labMap !== 'string') {
    const visitedCells = computeGuard();
    res = visitedCells.size;
  }
</script>

<h2>part 2</h2>
<div>obstructions: {res}</div>
