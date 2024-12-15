<script lang="ts">
const { list } = $props();
let res: number = $state(0);

// count the appearance of every stone
const stones: Map<string, number> = new Map();

function incrementCount(key: string, increment = 1) {
	const val = stones.get(key);
	stones.set(key, val ? val + increment : increment);
}

function decrementCount(key, decrement = 1) {
	const val = stones.get(key);
	if (val === 1 || val === decrement) stones.delete(key);
	else stones.set(key, val - decrement);
}

function printStones(): void {
	console.groupCollapsed();
	for (const key of stones.keys()) {
		console.log(key, stones.get(key));
	}
	console.groupEnd();
}

function blink(depth: number): number {
	for (let i = 0; i < depth; i++) {
		console.log("depth:", i);
		// printStones();
		for (const [key, val] of [...stones.entries()]) {
			if (key === "0") {
				// rule 1
				incrementCount("1", val);
			} else if (key.length % 2 === 0) {
				// rule 2
				const half = key.length / 2;
				incrementCount(`${+key.slice(0, half)}`, val);
				incrementCount(`${+key.slice(half)}`, val);
			} else {
				// rule 3
				incrementCount(`${+key * 2024}`, val);
			}
			decrementCount(key, val);
		}
	}

	let size = 0;
	for (const num of stones.values()) {
		size += num;
	}
	return size;
}

if (typeof list !== "string") {
	for (const num of list) {
		incrementCount(num);
	}
	res = blink(75);
}
</script>

<h2>part 2</h2>
<div>number of stones - depth 75: {res}</div>
