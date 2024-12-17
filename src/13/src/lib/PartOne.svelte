<script lang="ts">
const { equations } = $props();
let res: number = $state(0);
res = 0;

if (typeof equations !== "string") {
	for (const eq of equations) {
		const detA = eq.buttonA.x * eq.buttonB.y - eq.buttonB.x * eq.buttonA.y;
		const detAka = eq.prize.x * eq.buttonB.y - eq.buttonB.x * eq.prize.y;
		const detAkb = eq.buttonA.x * eq.prize.y - eq.prize.x * eq.buttonA.y;
		const a = detAka / detA;
		const b = detAkb / detA;
		console.log(
			"detA",
			detA,
			"solution x",
			detAka / detA,
			"solution y",
			detAkb / detA,
		);
		if (detA === 0 || !Number.isInteger(a) || !Number.isInteger(b)) continue;
		const solution = a * 3 + b;
		res += solution;
	}
}
</script>

<h2>part 1</h2>
<div>solutions sum: {res}</div>
