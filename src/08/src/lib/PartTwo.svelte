<script lang="ts">
const { grid } = $props();

interface Position {
	x: number;
	y: number;
}

const antennas: Map<string, Position[]> = new Map();
const antinodes: Set<string> = new Set();

function mapAntennnas() {
	for (const [j, line] of grid.entries()) {
		for (const [i, char] of line.entries()) {
			if (char !== ".") {
				if (antennas.has(char))
					antennas.set(char, antennas.get(char).concat([{ x: i, y: j }]));
				else antennas.set(char, [{ x: i, y: j }]);
			}
		}
	}
}

function getVector(from: Position, to: Position): Position {
	return { x: to.x - from.x, y: to.y - from.y };
}

function applyVector(start: Position, vector: Position): Position {
	return { x: start.x + vector.x, y: start.y + vector.y };
}

function isInGrid(pos: Position): boolean {
	const checkx: boolean = pos.x >= 0 && pos.x < grid[0].length;
	const checky: boolean = pos.y >= 0 && pos.y < grid.length;
	return checkx && checky;
}

function computeAntinodes(frequency: string) {
	const antPos: Position[] = antennas.get(frequency);

	// build combinations
	const combinations: Position[][] = [];
	for (let i = 0; i < antPos.length; i++) {
		for (let j = i + 1; j < antPos.length; j++) {
			combinations.push([antPos[i], antPos[j]]);
		}
	}

	// try for antinodes in both sides
	for (const combination of combinations) {
		console.group();
		const from: Position = { x: 0, y: 0 };
		const to: Position = { x: 0, y: 0 };
		from.x = combination[0].x;
		from.y = combination[0].y;
		to.x = combination[1].x;
		to.y = combination[1].y;
		const vector: Position = getVector(from, to);
		console.log(frequency, "combination", from, to);
		console.log("vector", vector);

		let candidate: Position;
		while (true) {
			candidate = applyVector(
				{ x: from.x, y: from.y },
				{ x: -vector.x, y: -vector.y },
			);
			console.log("from", from, "ingrid ?", candidate, isInGrid(candidate));
			if (isInGrid(candidate)) antinodes.add(`x${candidate.x}y${candidate.y}`);
			else break;
			from.x = candidate.x;
			from.y = candidate.y;
		}
		while (true) {
			candidate = applyVector(
				{ x: from.x, y: from.y },
				{ x: vector.x, y: vector.y },
			);
			console.log("from", from, "ingrid ?", candidate, isInGrid(candidate));
			if (isInGrid(candidate)) antinodes.add(`x${candidate.x}y${candidate.y}`);
			else break;
			from.x = candidate.x;
			from.y = candidate.y;
		}
		while (true) {
			candidate = applyVector(
				{ x: to.x, y: to.y },
				{ x: vector.x, y: vector.y },
			);
			if (isInGrid(candidate)) antinodes.add(`x${candidate.x}y${candidate.y}`);
			else break;
			to.x = candidate.x;
			to.y = candidate.y;
		}
		while (true) {
			candidate = applyVector(
				{ x: to.x, y: to.y },
				{ x: -vector.x, y: -vector.y },
			);
			if (isInGrid(candidate)) antinodes.add(`x${candidate.x}y${candidate.y}`);
			else break;
			to.x = candidate.x;
			to.y = candidate.y;
		}
		console.groupEnd();
	}
}

if (typeof grid !== "string") {
	mapAntennnas();
	for (const frequency of antennas.keys()) {
		computeAntinodes(frequency);
	}
}
</script>

<h2>part 2</h2>
<div>harmonics antinodes: {antinodes.size}</div>
