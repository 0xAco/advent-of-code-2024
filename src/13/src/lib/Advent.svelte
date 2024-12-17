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
interface Equation {
	buttonA: Vector;
	buttonB: Vector;
	prize: Position;
}
let equations: Equation[] = [];

function parse(input: string) {
	const parsed = input
		.replaceAll(/[A-Za-z]|:|=|\+|\h/gm, "")
		.split("\n")
		.map((l) => l.replaceAll(/\s/g, ""));

	let equation: Equation = {
		buttonA: { x: null, y: null },
		buttonB: { x: null, y: null },
		prize: { x: null, y: null },
	};
	parsed.forEach((line, i) => {
		const split = line.split(",");
		switch (i % 4) {
			case 0:
				equation.buttonA = { x: +split[0], y: +split[1] };
				break;
			case 1:
				equation.buttonB = { x: +split[0], y: +split[1] };
				break;
			case 2:
				equation.prize = { x: +split[0], y: +split[1] };
				break;
			default:
				equations.push(equation);
				equation = {
					buttonA: { x: null, y: null },
					buttonB: { x: null, y: null },
					prize: { x: null, y: null },
				};
				break;
		}
	});
}

onMount(async () => {
	let text = "";
	const response = await fetch("input.txt");
	// const response = await fetch("example.txt");
	if (response.ok) text = await response.text();
	else console.error("failed to load the file.");

	parse(text);
	// biome-ignore lint/correctness/noSelfAssign: <explanation>
	equations = equations; // need this for svelte reactive
});
</script>

{#if equations.length > 0}
  <PartOne equations="{equations}" />
  <PartTwo equations="{equations}" />
{:else}
  <PartOne equations="loading..." />
  <PartTwo equations="loading..." />
{/if}