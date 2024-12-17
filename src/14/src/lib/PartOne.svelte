<script lang="ts">
const { inputs } = $props();
let res: number = $state(0);

const { robots, size } = inputs;

function computeResults(): number {
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
	return q1sum * q2sum * q3sum * q4sum;
}

function simulate(seconds: number) {
	for (const robot of inputs.robots) {
		for (let i = 0; i < seconds; i++) {
			const newx = (robot.pos.x + robot.move.x) % inputs.size[0];
			const newy = (robot.pos.y + robot.move.y) % inputs.size[1];
			robot.pos.x = newx >= 0 ? newx : size[0] + newx;
			robot.pos.y = newy >= 0 ? newy : size[1] + newy;
		}
	}
}

if (typeof inputs !== "string") {
	computeResults();
	simulate(100);
	res = computeResults();
}
</script>

<h2>part 1</h2>
<div> sum of quadrants: {res}</div>
