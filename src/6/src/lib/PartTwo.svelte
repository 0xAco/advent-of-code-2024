<script lang="ts">
  const { labMap } = $props();
  let res: number = $state(0);

  type Direction = 'N' | 'E' | 'S' | 'W';
  type Position = {
    x: number,
    y: number,
    d?: Direction
  };
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

  function tryLoop(newObstacle: Position, guardPos: Position, direction: Direction, bonks: Set<string>) {
    const loopBonks: Set<string> = new Set();
    let nextPos: Position = null;

    // try putting an obstacle in front
    labMap[newObstacle.x][newObstacle.y] = '#';

    const timeout = 5000;
    for (let i = 0; i < timeout; i++) {
      nextPos = getNextCell(guardPos, direction);
      if (labMap[nextPos.x][nextPos.y] === '#') {
        const bonk = `x${guardPos.x}y${guardPos.y}d${direction}`;
        // loop confirmed if we bonk twice on the
        // same obstacle with the same direction
        if (loopBonks.has(bonk) || bonks.has(bonk)) {
          console.log(newObstacle);
          return true;
        }
        loopBonks.add(bonk);
        direction = rotateRight(direction);
      } else {
        guardPos.x = nextPos.x
        guardPos.y = nextPos.y;
      }

      // loop breaks early
      const breakPos:Position = getNextCell(guardPos, direction);
      const checkx:boolean = breakPos.x >= 0 && breakPos.x < labMap.length;
      const checky:boolean = breakPos.y >= 0 && breakPos.y < labMap[0].length;
      if (!checkx || !checky) {
        // remove the obstacle
        labMap[newObstacle.x][newObstacle.y] = '';
        return false;
      }
    }

    return false;
  }

  function computeGuard() {
    const visited: Set<string> = new Set();
    const bonks: Set<string> = new Set();
    let guardPos: Position = findGuard();
    let direction: Direction = 'N';

    visited.add(`x${guardPos.x}y${guardPos.y}`);

    let nextPos: Position = null;
    do {
      nextPos = getNextCell(guardPos, direction);
      if (labMap[nextPos.x][nextPos.y] === '#') {
        bonks.add(`x${guardPos.x}y${guardPos.y}d${direction}`);
        direction = rotateRight(direction);
      } else {
        const obstacle = `x${nextPos.x}y${nextPos.y}`;
        if (!bonks.has(obstacle)) {
          const isloop = tryLoop(nextPos, guardPos, direction, bonks);
          checked.add(obstacle);
          if (isloop) res ++;
        }

        visited.add(`x${guardPos.x}y${guardPos.y}`);

        // update map for visuals
        // labMap[guardPos.x][guardPos.y] = 'X';
        
        // update for nextPos loop
        guardPos.x = nextPos.x
        guardPos.y = nextPos.y;

        labMap[guardPos.x][guardPos.y] = 'o';

        // console.group();
        // for (const row of labMap) console.log(row);
        // console.groupEnd();
      }
    } while(getNextCell(guardPos, direction).x >= 0 && getNextCell(guardPos, direction).x < labMap.length && getNextCell(guardPos, direction).y >= 0 && getNextCell(guardPos, direction).y < labMap[0].length);

    visited.add('x' + guardPos.x + 'y' + guardPos.y);
  }

  if (typeof labMap !== 'string') {
    computeGuard();
  }
</script>

<h2>part 2</h2>
<div>obstructions: {res}</div>
