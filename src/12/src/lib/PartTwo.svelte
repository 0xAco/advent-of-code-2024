<script lang="ts">
const { garden } = $props();
let res: number = $state(0);

interface Position {
	x: number; // row
	y: number; // col
}
interface Area {
	plots: Position[];
	perimeter: number;
	sides: number;
}
type Direction = "N" | "E" | "S" | "W" | "NE" | "NW" | "SE" | "SW";
const directions: Direction[] = ["N", "E", "S", "W", "NE", "NW", "SE", "SW"];
const done: Set<string> = new Set();
const areas: Area[] = [];

function getAdjacent(from: Position, direction: Direction): Position {
	switch (direction) {
		case "N":
			if (from.x === 0) return null;
			return { x: from.x - 1, y: from.y };
		case "E":
			if (from.y === garden[0].length - 1) return null;
			return { x: from.x, y: from.y + 1 };
		case "S":
			if (from.x === garden.length - 1) return null;
			return { x: from.x + 1, y: from.y };
		case "W":
			if (from.y === 0) return null;
			return { x: from.x, y: from.y - 1 };
	}
}

function getAdjacentPlant(from: Position, direction: Direction): string {
	switch (direction) {
		case "N":
			if (from.x === 0) return null;
			return garden[from.x - 1][from.y];
		case "E":
			if (from.y === garden[0].length - 1) return null;
			return garden[from.x][from.y + 1];
		case "S":
			if (from.x === garden.length - 1) return null;
			return garden[from.x + 1][from.y];
		case "W":
			if (from.y === 0) return null;
			return garden[from.x][from.y - 1];
		case "NE":
			if (from.x === 0 || from.y === garden[0].length - 1) return null;
			return garden[from.x - 1][from.y + 1];
		case "NW":
			if (from.x === 0 || from.y === 0) return null;
			return garden[from.x - 1][from.y - 1];
		case "SE":
			if (from.x === garden.length - 1 || from.y === garden[0].length - 1)
				return null;
			return garden[from.x + 1][from.y + 1];
		case "SW":
			if (from.x === garden.length - 1 || from.y === 0) return null;
			return garden[from.x + 1][from.y - 1];
	}
}

function checkCorners(plot: Position): number {
	let corners = 0;
	const p = garden[plot.x][plot.y];
	const n = getAdjacentPlant(plot, "N");
	const ne = getAdjacentPlant(plot, "NE");
	const e = getAdjacentPlant(plot, "E");
	const se = getAdjacentPlant(plot, "SE");
	const s = getAdjacentPlant(plot, "S");
	const sw = getAdjacentPlant(plot, "SW");
	const w = getAdjacentPlant(plot, "W");
	const nw = getAdjacentPlant(plot, "NW");
	if ((p === n && p === e && p !== ne) || (p !== n && p !== e)) corners++; // top right corner
	if ((p === s && p === e && p !== se) || (p !== s && p !== e)) corners++; // bottom right corner
	if ((p === s && p === w && p !== sw) || (p !== s && p !== w)) corners++; // bottom left corner
	if ((p === n && p === w && p !== nw) || (p !== n && p !== w)) corners++; // top left corner
	return corners;
}

function buildArea(plant: string, area: Area, plotsToCheck: Position[]) {
	if (plotsToCheck.length === 0) return area;
	const nextPlots: Position[] = [];
	for (const plot of plotsToCheck) {
		area.sides += checkCorners(plot);
		// search every direction
		for (const direction of directions) {
			const adj = getAdjacent(plot, direction);
			if (!adj) continue;
			const adjPlant = garden[adj.x][adj.y];
			if (done.has(`x${adj.x}y${adj.y}`)) continue;
			if (adjPlant === plant) {
				nextPlots.push({ x: adj.x, y: adj.y });
				area.plots.push({ x: adj.x, y: adj.y });
				done.add(`x${adj.x}y${adj.y}`);
			}
		}
	}
	return buildArea(plant, area, nextPlots);
}

function computeResults(): number {
	let result = 0;
	for (const area of areas) {
		result += area.plots.length * area.sides;
	}
	return result;
}

function printGarden() {
	console.log(garden.map((line) => line.join("")).join("\n"));
}

function printAreas() {
	for (const area of areas) {
		const plant = garden[area.plots[0].x][area.plots[0].y];
		const size = area.plots.length;
		const perimeter = area.perimeter;
		const sides = area.sides;
		console.groupCollapsed(plant, size, perimeter, sides);
		console.log(area.plots);
		console.groupEnd();
	}
}

if (typeof garden !== "string") {
	printGarden();
	for (const [i, line] of garden.entries()) {
		for (const [j, plot] of line.entries()) {
			if (done.has(`x${i}y${j}`)) continue;
			done.add(`x${i}y${j}`);
			areas.push(
				buildArea(
					garden[i][j],
					{ plots: [{ x: i, y: j }], perimeter: 0, sides: 0 },
					[{ x: i, y: j }],
				),
			);
		}
	}
	// printAreas();
	res = computeResults();
}
</script>

<h2>part 2</h2>
<div>fence total price discounted by sides: {res}</div>