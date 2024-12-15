<script lang="ts">
const { calibrations } = $props();
let res: number = $state(0);

type Calibration = Map<number, number[]>;
type Operator = "+" | "*" | "|";

const operators: Operator[] = ["+", "*", "|"];

function testCalibration(calibration: Calibration) {
	const target: number = calibration[0];
	const values: number[] = calibration[1];
	let opCombinations: Operator[][] = operators.map((op) => [op]);

	// depth of each combination (= length of operators arrays)
	const combLength: number = values.length - 1;
	for (let j = 1; j < combLength; j++) {
		opCombinations = opCombinations.flatMap((combo) =>
			operators.map((op) => [...combo, op]),
		);
	}

	// compute every combination
	for (const opCombination of opCombinations) {
		let result: number = values[0];
		for (const [i, op] of opCombination.entries()) {
			switch (op) {
				case "+":
					result += values[i + 1];
					break;
				case "*":
					result *= values[i + 1];
					break;
				case "|":
					result = +`${result}${values[i + 1]}`;
			}
		}
		if (result === target) {
			return true;
		}
	}
	return false;
}

if (typeof calibrations !== "string") {
	for (const calibration of calibrations) {
		if (testCalibration(calibration)) {
			res += calibration[0];
		}
	}
}
</script>

<h2>part 2</h2>
<div>good calibrations sum (using concat): {res}</div>