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

const doubleMap: string[][] = $state([]);

function doubleTheMap() {
	const doubleLine = [];
	for (const line of inputs.map) {
		for (const el of line) {
			switch (el) {
				case "#":
					doubleLine.push("#", "#");
					break;
				case "@":
					doubleLine.push("@", ".");
					break;
				case "O":
					doubleLine.push("[", "]");
					break;
				default:
					doubleLine.push(".", ".");
					break;
			}
		}
		doubleMap.push([...doubleLine]);
		doubleLine.length = 0;
	}
}

function printMap() {
	console.log(doubleMap.map((line) => line.join("")).join("\n"));
}

function getRoverPos(): Position {
	for (const [i, line] of doubleMap.entries()) {
		for (const [j, el] of line.entries()) {
			if (el === "@") return { x: j, y: i };
		}
	}
	return null;
}

function computeGPS() {
	let sum = 0;
	for (const [i, line] of doubleMap.entries()) {
		for (const [j, el] of line.entries()) {
			if (el === "[") sum += 100 * i + j;
		}
	}
	return sum;
}

// returns the next available space
// returns null of none are found
function scout(from, direction): Position {
	const target: Position = { x: from.x, y: from.y };
	const current: Position = { x: from.x, y: from.y };
	let targetEl = doubleMap[target.y][target.x];
	const vector = directions.get(direction);
	let timeout = 0;
	do {
		target.x = current.x + vector[0];
		target.y = current.y + vector[1];
		current.x = target.x;
		current.y = target.y;
		targetEl = doubleMap[target.y][target.x];
		if (targetEl === ".") return target;
		timeout++;
	} while (targetEl !== "#" && targetEl !== "." && timeout < 10);
	return null;
}

function getPos(from, direction): Position {
	const vector = directions.get(direction);
	return { x: from.x + vector[0], y: from.y + vector[1] };
}

function getElement(pos: Position): string {
	return doubleMap[pos.y][pos.x];
}

// checks if the rover can push
// check every collision
function canPush(from, direction) {
	let el = getElement(from);
	const toCheck: Position[] = [from];
	const toMove = [{ pos: from, el }];

	while (toCheck.length > 0) {
		for (const [i, pos] of toCheck.entries()) {
			el = getElement(pos);
			const verticalPos = getPos(pos, direction);
			const verticalEl = getElement(verticalPos);
			if (verticalEl === "#") return null;
			if (verticalEl === "[" || verticalEl === "]") {
				toCheck.push({ ...verticalPos });
				toMove.push({ pos: verticalPos, el: verticalEl });
			}
			// find the other half of the box
			if (el === "[") {
				const rightPos = getPos(pos, ">");
				if (
					toMove.findIndex(
						(obj) => obj.pos.x === rightPos.x && obj.pos.y === rightPos.y,
					) === -1
				) {
					toCheck.push(rightPos);
					toMove.push({ pos: rightPos, el: getElement(rightPos) });
				}
			} else if (el === "]") {
				const leftPos = getPos(pos, "<");
				if (
					toMove.findIndex(
						(obj) => obj.pos.x === leftPos.x && obj.pos.y === leftPos.y,
					) === -1
				) {
					toCheck.push(leftPos);
					toMove.push({ pos: leftPos, el: getElement(leftPos) });
				}
			}

			// remove this element as it's been treated
			toCheck.splice(i, 1);
		}
	}
	return toMove;
}

function move(from: Position, direction: Direction) {
	const vector = directions.get(direction);
	const target: Position = {
		x: from.x + vector[0],
		y: from.y + vector[1],
	};
	const targetEl = doubleMap[target.y][target.x];

	switch (targetEl) {
		// ignore instruction
		case "#":
			return;
		case ".": // move rover
			rover = { x: target.x, y: target.y };
			doubleMap[target.y][target.x] = "@";
			doubleMap[from.y][from.x] = ".";
			return;
		default: {
			// push boxes if possible
			if (direction === "<" || direction === ">") {
				// push boxes on x axis
				const nextSpace = scout(from, direction);
				if (nextSpace) {
					rover = { x: target.x, y: target.y };
					// update from nextSpace to target on x
					for (let i = 0; i < Math.abs(target.x - nextSpace.x); i++) {
						const primer = direction === "<" ? nextSpace.x : target.x + 1;
						doubleMap[target.y][primer + i] = i % 2 === 0 ? "[" : "]";
					}
					doubleMap[target.y][target.x] = "@";
					doubleMap[from.y][from.x] = ".";
					return;
				}
			} else {
				// push boxes on y axis
				const boxesToMove = canPush(from, direction);
				if (boxesToMove) {
					for (const box of boxesToMove) doubleMap[box.pos.y][box.pos.x] = ".";
					for (const box of boxesToMove) {
						const vector = directions.get(direction);
						doubleMap[box.pos.y + vector[1]][box.pos.x] = box.el;
					}
					rover = { x: target.x, y: target.y };
					doubleMap[from.y][from.x] = ".";
				}
			}
		}
	}
}

if (typeof inputs !== "string") {
	console.group("part 2");
	doubleTheMap();
	rover = getRoverPos();
	for (const instruction of inputs.instructions) {
		move(rover, instruction);
		console.log(instruction);
		printMap();
	}
	printMap();
	res = computeGPS();
	console.groupEnd();
}
</script>

<h2>part 2</h2>
<div>sum of gps: {res}</div>
