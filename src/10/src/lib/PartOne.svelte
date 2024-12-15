<script lang="ts">
const { topmap } = $props();
let res: number = $state(0);

type Direction = "N" | "E" | "S" | "W";
const directions: Direction[] = ["N", "E", "S", "W"];
interface Position {
	x: number; // row index
	y: number; // col index
}
const starts: Position[] = [];

function printMap() {
	console.group();
	console.log(topmap.map((row) => row.join("")).join("\n"));
	console.groupEnd();
}

function getAdjacent(from: Position, direction: Direction): Position {
	switch (direction) {
		case "N":
			if (from.x === 0) return null;
			return { x: from.x - 1, y: from.y };
		case "E":
			if (from.y === topmap[0].length - 1) return null;
			return { x: from.x, y: from.y + 1 };
		case "S":
			if (from.x === topmap.length - 1) return null;
			return { x: from.x + 1, y: from.y };
		case "W":
			if (from.y === 0) return null;
			return { x: from.x, y: from.y - 1 };
	}
}

function buildTrailheads(searchVal: number, lastPositions: Position[]) {
	if (searchVal > 9) return lastPositions;
	if (searchVal) {
		const nextPositions: Position[] = [];
		for (const position of lastPositions) {
			// search every direction
			for (const direction of directions) {
				const adj = getAdjacent(position, direction);
				const adjVal = adj ? topmap[adj.x][adj.y] : null;
				if (adjVal === searchVal) nextPositions.push({ x: adj.x, y: adj.y });
			}
		}
		return buildTrailheads(searchVal + 1, nextPositions);
	}
}

if (typeof topmap !== "string") {
	// printMap();

	// find every start
	for (const [i, row] of topmap.entries()) {
		for (const [j, val] of row.entries()) {
			if (val === 0) starts.push({ x: i, y: j });
		}
	}

	// console.log(0, starts);
	const paths: Map<string, string[]> = new Map();
	for (const start of starts) {
		const routes: string[] = buildTrailheads(1, [start]).map(
			(pos) => `x${pos.x}y${pos.y}`,
		);
		const uniqueEnds: Set<string> = new Set(routes);
		res += uniqueEnds.size;
		paths.set(`x${start.x}y${start.y}`, Array.from(uniqueEnds));
	}
}
</script>

<h2>part 1</h2>
<div>number of reachables: {res}</div>