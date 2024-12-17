<script lang="ts">
import PartOne from "./PartOne.svelte";
import PartTwo from "./PartTwo.svelte";
import { onMount } from "svelte";

interface Position {
	x: number;
	y: number;
}
interface Vector {
	x: number;
	y: number;
}
interface Robot {
	pos: Position;
	move: Vector;
}
const robots: Robot[] = [];
const inputs = {
	robots,
	size: [null, null],
};

function parse(input) {
	const split = input
		.replaceAll(/[A-Za-z]|=|\h/gm, "")
		.split("\n")
		.map((line) => line.split(" "));
	for (const botInput of split) {
		let robot = {
			pos: { x: 0, y: 0 },
			move: { x: 0, y: 0 },
		};
		botInput.forEach((values, i) => {
			const [x, y] = values.split(",").map((a) => +a);
			if (i % 2 === 0) {
				robot.pos.x = x;
				robot.pos.y = y;
			} else {
				robot.move.x = x;
				robot.move.y = y;
			}
		});
		robots.push(robot);
		robot = {
			pos: { x: 0, y: 0 },
			move: { x: 0, y: 0 },
		};
	}
}

onMount(async () => {
	let text = "";
	const input: string = "input.txt";
	const response = await fetch(input);
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	inputs.size = input === "input.txt" ? [101, 103] : [11, 7];
	parse(text);
});
</script>

{#if inputs.robots.length > 0}
  <PartOne inputs="{inputs}" />
  <PartTwo inputs="{inputs}" />
{:else}
  <PartOne inputs="loading..." />
  <PartTwo inputs="loading..." />
{/if}