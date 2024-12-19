<script lang="ts">
const { inputs } = $props();
let res: number = $state(0);

interface Position {
	x: number;
	y: number;
}
type Direction = "^" | ">" | "v" | "<";
const directions: Map<Direction, number[]> = new Map();
directions.set("^", [0, -1]);
directions.set(">", [1, 0]);
directions.set("v", [0, 1]);
directions.set("<", [-1, 0]);

let rover: Position;
const map: string[][] = [];

function copyMap() {
	for (const line of inputs.map) {
		map.push([...line]);
	}
}

function printMap() {
	console.log(map.map((line) => line.join("")).join("\n"));
}

function getRoverPos(): Position {
	for (const [i, line] of map.entries()) {
		for (const [j, el] of line.entries()) {
			if (el === "@") return { x: i, y: j };
		}
	}
	return null;
}

function computeGPS() {
	let sum = 0;
	for (const [i, line] of map.entries()) {
		for (const [j, el] of line.entries()) {
			if (el === "O") sum += 100 * i + j;
		}
	}
	return sum;
}

// returns the next available space
// returns null of none are found
function scout(from, direction): Position {
	const target: Position = { x: from.x, y: from.y };
	const current: Position = { x: from.x, y: from.y };
	let targetEl = map[target.y][target.x];
	const vector = directions.get(direction);
	let timeout = 0;
	do {
		target.x = current.x + vector[0];
		target.y = current.y + vector[1];
		current.x = target.x;
		current.y = target.y;
		targetEl = map[target.y][target.x];
		if (targetEl === ".") return target;
		timeout++;
	} while (targetEl !== "#" && targetEl !== "." && timeout < 10);
	return null;
}

function move(from: Position, direction: Direction) {
	const vector = directions.get(direction);
	const target: Position = {
		x: from.x + vector[0],
		y: from.y + vector[1],
	};

	switch (map[target.y][target.x]) {
		case "#": // ignore instruction
			return false;
		case ".": // move rover
			rover = { x: target.x, y: target.y };
			map[target.y][target.x] = "@";
			map[from.y][from.x] = ".";
			return true;
		default: {
			// push boxes if possible
			const nextSpace = scout(from, direction);
			if (nextSpace) {
				rover = { x: target.x, y: target.y };
				map[nextSpace.y][nextSpace.x] = "O";
				map[target.y][target.x] = "@";
				map[from.y][from.x] = ".";
				return true;
			}
			return false;
		}
	}
}

if (typeof inputs !== "string") {
	console.groupCollapsed("part 1");
	copyMap();
	rover = getRoverPos();
	for (const instruction of inputs.instructions) {
		move(rover, instruction);
	}
	res = computeGPS();
	console.groupEnd();
}
</script>

<h2>part 1</h2>
<div>sum of gps: {res}</div>
