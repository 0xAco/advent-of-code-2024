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
		const [from, to] = combination;
		const vector: Position = getVector(from, to);

		const candidates = [
			applyVector(to, vector),
			applyVector(from, { x: -vector.x, y: -vector.y }),
		];

		if (isInGrid(candidates[0]))
			antinodes.add(`x${candidates[0].x}y${candidates[0].y}`);
		if (isInGrid(candidates[1]))
			antinodes.add(`x${candidates[1].x}y${candidates[1].y}`);
	}
}

if (typeof grid !== "string") {
	mapAntennnas();
	for (const frequency of antennas.keys()) {
		computeAntinodes(frequency);
	}
}
</script>

<h2>part 1</h2>
<div>antinodes: {antinodes.size}</div>
