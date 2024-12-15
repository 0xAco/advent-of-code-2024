<script lang="ts">
const { list } = $props();
let res: number = $state(0);

function blink(input: string[]): string[] {
	const output: string[] = [];

	for (const str of input) {
		// rule 1
		if (str === "0") {
			output.push("1");
			continue;
		}

		// rule 2
		if (str.length % 2 === 0) {
			output.push(`${+str.slice(0, str.length / 2)}`);
			output.push(`${+str.slice(str.length / 2)}`);
			continue;
		}

		// rule 3
		output.push(`${+str * 2024}`);
	}

	return output;
}

if (typeof list !== "string") {
	let step: string[] = [...list];

	for (let i = 0; i < 25; i++) step = blink(step);
	res = step.length;
}
</script>

<h2>part 1</h2>
<div>number of stones - depth 25: {res}</div>
