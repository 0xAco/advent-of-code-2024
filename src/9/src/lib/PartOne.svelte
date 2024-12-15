<script lang="ts">
const { list } = $props();
let res: number = $state(0);

function getMemory(): string[] {
	const memory = [];
	let currentId = 0;
	for (let i = 0; i < list.length; i++) {
		const toadd = Array(list[i]).fill(i % 2 === 0 ? `${currentId}` : ".");
		if (toadd.length) memory.push(toadd);
		if (i % 2 === 0) currentId++;
	}
	return memory.flat();
}

function compactMemory(memory: string[]): string[] {
	for (let i = memory.length - 1; i >= 0; i--) {
		// for each number starting from the end
		if (memory[i] !== ".") {
			// search for a new place
			const free = memory.indexOf(".");
			if (free > i) break;
			if (free > -1) {
				memory[free] = memory[i];
				memory[i] = ".";
			}
		}
	}
	return memory;
}

function computeChecksum(compact: string[]): number {
	let checksum = 0;
	for (const [i, num] of compact.entries()) {
		checksum += num === "." ? 0 : i * +num;
	}
	return checksum;
}

if (typeof list !== "string") {
	const memory: string[] = getMemory();
	const compact: string[] = compactMemory(memory);
	res = computeChecksum(compact);
}
</script>

<h2>part 1</h2>
<div>checksum: {res}</div>
