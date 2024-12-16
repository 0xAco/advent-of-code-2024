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
}
type Direction = "N" | "E" | "S" | "W";
const directions: Direction[] = ["N", "E", "S", "W"];
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

function buildArea(plant: string, area: Area, plotsToCheck: Position[]) {
	if (plotsToCheck.length === 0) return area;
	const nextPlots: Position[] = [];
	for (const plot of plotsToCheck) {
		// search every direction
		for (const direction of directions) {
			const adj = getAdjacent(plot, direction);
			if (!adj) {
				// garden border, increase perimeter
				area.perimeter++;
				continue;
			}
			const adjPlant = garden[adj.x][adj.y];
			if (done.has(`x${adj.x}y${adj.y}`)) {
				if (adjPlant !== plant) area.perimeter++;
				continue;
			}
			if (adjPlant === plant) {
				nextPlots.push({ x: adj.x, y: adj.y });
				area.plots.push({ x: adj.x, y: adj.y });
				done.add(`x${adj.x}y${adj.y}`);
			} else area.perimeter++;
		}
	}
	return buildArea(plant, area, nextPlots);
}

function computeResults(): number {
	let result = 0;
	for (const area of areas) {
		result += area.plots.length * area.perimeter;
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
		console.groupCollapsed(plant, size, perimeter);
		console.log("plant: ", plant);
		console.log(area.plots);
		console.log("surface: ", area.plots.length);
		console.log("perimeter: ", area.perimeter);
		console.groupEnd();
	}
}

if (typeof garden !== "string") {
	// printGarden();
	for (const [i, line] of garden.entries()) {
		for (const [j, plot] of line.entries()) {
			if (done.has(`x${i}y${j}`)) continue;
			done.add(`x${i}y${j}`);
			areas.push(
				buildArea(garden[i][j], { plots: [{ x: i, y: j }], perimeter: 0 }, [
					{ x: i, y: j },
				]),
			);
		}
	}
	// printAreas();
	res = computeResults();
}
</script>

<h2>part 1</h2>
<div>fence total price: {res}</div>