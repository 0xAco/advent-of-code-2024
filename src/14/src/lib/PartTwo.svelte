<script lang="ts">
const { inputs } = $props();
const res: number = $state(0);

const { robots, size } = inputs;

function computeResults(iteration): number {
	let q1sum = 0;
	let q2sum = 0;
	let q3sum = 0;
	let q4sum = 0;
	let map = "";
	for (let i = 0; i < size[1]; i++) {
		for (let j = 0; j < size[0]; j++) {
			// number of bots that are in this cell
			const num = robots.filter(
				(bot) => bot.pos.x === j && bot.pos.y === i,
			).length;
			map += num > 0 ? num : ".";
			if (j < Math.floor(size[0] / 2) && i < Math.floor(size[1] / 2))
				q1sum += num;
			else if (j >= Math.ceil(size[0] / 2) && i < Math.floor(size[1] / 2))
				q2sum += num;
			else if (j < Math.floor(size[0] / 2) && i >= Math.ceil(size[1] / 2))
				q3sum += num;
			else if (j >= Math.ceil(size[0] / 2) && i >= Math.ceil(size[1] / 2))
				q4sum += num;
		}
		map += "\n";
	}
	const threshold = 250;
	if (
		q1sum >= threshold ||
		q2sum >= threshold ||
		q3sum >= threshold ||
		q4sum >= threshold
	) {
		console.groupCollapsed("SAPING ?", iteration + 100); // +100 because we simulate part 1 as well
		console.log(map);
		console.groupEnd();
	}
	return q1sum * q2sum * q3sum * q4sum;
}

function simulate() {
	for (const robot of robots) {
		const newx = (robot.pos.x + robot.move.x) % inputs.size[0];
		const newy = (robot.pos.y + robot.move.y) % inputs.size[1];
		robot.pos.x = newx >= 0 ? newx : size[0] + newx;
		robot.pos.y = newy >= 0 ? newy : size[1] + newy;
	}
	// find bots that form a horizontal line
	// display triggers when at least 3 bots align
	robots.sort((bota, botb) => bota.pos.y - botb.pos.y);
	let prev1 = null;
	let prev2 = null;
	for (const robot of robots) {
		if ((robot.pos.x + prev1 + prev2) % 3 === 0) {
			return true;
		}
		prev2 = prev1;
		prev1 = robot.pos.x;
	}
	return false;
}

if (typeof inputs !== "string") {
	console.log("start");
	const limit = 10000;
	for (let i = 1; i <= limit; i++) {
		simulate();
		if (i === 7747) computeResults(i); // 7847 was the result
	}
	console.log("done");
}
</script>

<h2>part 1</h2>
<div>christmas tree formation :D : {res}</div>
