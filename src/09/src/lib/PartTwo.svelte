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

function findFreeSpace(arr: string[], size) {
	for (let i = 0; i < arr.length - size; i++) {
		if (arr.slice(i, i + size).every((val) => val === ".")) {
			return i;
		}
	}
	return -1;
}

function compactMemory(memory: string[]): string[] {
	let prevId = null;
	let offset = 0;
	let startIndex = memory.length - 1;
	let size = 1;
	for (let i = memory.length - 1; i >= 0; i--) {
		const currentId = memory[i];
		if (currentId === "." && prevId === ".") {
			offset++;
			continue;
		}
		if (!prevId) {
			prevId = currentId;
			continue;
		}

		// find the size of the file
		// first value or same value as previous
		if (prevId === currentId) {
			prevId = currentId;
			size++;
			continue;
		}

		// different value
		// save the starting index of the file
		startIndex = i + 1 + offset;
		// find a new place
		const freeSpace = findFreeSpace(memory, size);
		if (freeSpace > -1 && freeSpace < i) {
			// move the file
			for (let j = freeSpace; j < freeSpace + size; j++) {
				memory[j] = prevId;
			}

			// remove the file from its original place
			for (let j = startIndex; j < startIndex + size; j++) {
				memory[j] = ".";
			}
		}

		// reset loop variables
		size = 1;
		prevId = memory[i];
		offset = 0;
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

<h2>part 2</h2>
<div>file integrity checksum: {res}</div>
