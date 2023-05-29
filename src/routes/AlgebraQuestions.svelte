<script>
import Katex from '../Katex.svelte'

function rand(max) {
    return Math.floor(Math.random() * max);
}

let symbols = ["a", "x", "m", "b", "y", "n", "c", "d", ""];

function generateNumsAndSyms() {
	let isSqRoot1 = rand(5) == 0;
	let isSqRoot2 = rand(5) == 0;
	let n1 = 1 + rand(8);
	while(isSqRoot1 && [1, 4, 9].includes(n1)) {
		n1 = 1 + rand(8);
	}
	let n2 = 1 + rand(8);
	while(isSqRoot2 && [1, 4, 9].includes(n2)) {
		n2 = 1 + rand(8);
	}

	let sym1 = symbols[rand(symbols.length)];
	let sym2 = symbols[rand(symbols.length)];
	while(sym1 == sym2) {
		sym2 = symbols[rand(symbols.length)];
	}
	return {
		n1: n1,
		isSqRoot1: isSqRoot1,
		n2: n2,
		isSqRoot2: isSqRoot2,
		sym1: sym1,
		sym2: sym2
	};
}

function combineNumAndSym(num, sym, isSqRoot = false) {
	if (num === 1 && sym != "") { return `${sym}` }
	let sq = (isSqRoot && num != 1) ? `\\sqrt{${num}}` : `${num}`;
	sq += (sym == "") ? "" : `\\cdot ${sym}`;
	return sq;
}

function combineSquareNumAndSym(num, sym, isSqRoot = false) {
	if (num === 1 && sym != "") { return `${sym}^2` }
	let sq = (isSqRoot && num != 1) ? `${num}` : `${num * num}`;
	sq += (sym == "") ? "" : `\\cdot ${sym}^2`;
	return sq;
}

function sumOrDiffSquared() {
	let {n1, isSqRoot1, n2, isSqRoot2, sym1, sym2} = generateNumsAndSyms();

	let sign = rand(2) == 0 ? "-" : "+";
	let t1 = combineNumAndSym(n1, sym1, isSqRoot1);
	let t2 = combineNumAndSym(n2, sym2, isSqRoot2);
	let lhs = `(${t1} ${sign} ${t2})^2`;
	let solLhs = `(\\underbrace{${t1}}_a ${sign} \\underbrace{${t2}}_b)^2`;

	let a1 = combineSquareNumAndSym(n1, sym1, isSqRoot1);
	let a2 = combineSquareNumAndSym(n2, sym2, isSqRoot2);

	let midTerm = "";
	if (isSqRoot1 && isSqRoot2) { midTerm = `2 \\cdot \\sqrt{${n1 * n2}}` }
	else if (isSqRoot1) { midTerm = `${2*n2} \\cdot \\sqrt{${n1}}` }
	else if (isSqRoot2) { midTerm = `${2*n1} \\cdot \\sqrt{${n2}}` }
	else                { midTerm = `${2 * n1 * n2}` }
	midTerm += "\\cdot ";
	if      (sym1 == "") { midTerm += `${sym2}` }
	else if (sym2 == "") { midTerm += `${sym1}` }
	else   {midTerm += `${sym1} \\cdot ${sym2}` }

	let rhs = `${a1} ${sign} ${midTerm} + ${a2}`;
	let solRhs = `\\underbrace{${a1}}_{a^2} ${sign} \\underbrace{${midTerm}}_{2 \\cdot a \\cdot b} + \\underbrace{${a2}}_{b^2}`;

	let task = "";
	if (rand(2) === 0) {
		task = "Ridica expresia la patrat:";
	}
	else {
		[lhs, rhs] = [rhs, lhs];
		[solLhs, solRhs] = [solRhs, solLhs];
		task = "Restrange expresia:";
	}

	return {
		lhs: lhs,
		rhs: rhs,
		solLhs: solLhs,
		solRhs: solRhs,
		hint: "(a+b)^2=a^2+2\\cdot a \\cdot b + b^2",
		task: task
	};
}

function diffBetweenSquared() {
	let {n1, isSqRoot1, n2, isSqRoot2, sym1, sym2} = generateNumsAndSyms();

	let a1 = combineSquareNumAndSym(n1, sym1, isSqRoot1);
	let a2 = combineSquareNumAndSym(n2, sym2, isSqRoot2);
	let lhs = `${a1} - ${a2}`;
	let solLhs = `\\underbrace{${a1}}_{a^2} - \\underbrace{${a2}}_{b^2}`;

	let t1 = combineNumAndSym(n1, sym1, isSqRoot1);
	let t2 = combineNumAndSym(n2, sym2, isSqRoot2);
	let rhs = `(${t1} - ${t2}) \\cdot (${t1} + ${t2})`;
	let solRhs = `(\\underbrace{${t1}}_a - \\underbrace{${t2}}_b) \\cdot (\\underbrace{${t1}}_a + \\underbrace{${t2}}_b)`;

	let task = "";
	if (rand(2) === 0) {
		task = "Rescrie expresia fara patrate:";
	}
	else {
		[lhs, rhs] = [rhs, lhs];
		[solLhs, solRhs] = [solRhs, solLhs];
		task = "Desfa parantezele:";
	}

	return {
		lhs: lhs,
		rhs: rhs,
		solLhs: solLhs,
		solRhs: solRhs,
		hint: "a^2 - b^2 = (a-b)\\cdot (a+b)",
		task: task
	};
}

function generateMathExpression() {
	return rand(2) === 0 ? diffBetweenSquared() : sumOrDiffSquared();
}

let math = generateMathExpression();

let isAnswer = false;
let isHint =  false;
let isApplyHint = false;
let pressNext = false;

function newQuestion() {
	isAnswer = false;
	isHint = false;
	isApplyHint = false;
	math = generateMathExpression();
	pressNext = false;
}
function toggleAnswer() {
	isAnswer = !isAnswer;
	pressNext = true;
}
function toggleHint() {
	isHint = true;
}
function applyHint() {
	isApplyHint = !isApplyHint;
	pressNext = true;
}

</script>

<div class="flex-container">
	<span>{math.task}</span>
	{#if !isAnswer}
		<Katex math={math.lhs + " = ..."} displayMode/>
	{:else}
		<Katex math={`${math.lhs} = \\green{${math.rhs}}`} displayMode/>
	{/if}

	<button on:click={toggleAnswer}>
		{#if isAnswer}
			Hide Answer
		{:else}
			Show Answer
		{/if}
	</button><br>

	<button on:click={toggleHint}>
		Show Hint
	</button><br>

	{#if isHint}
		<Katex math={math.hint} displayMode/>
		{#if isApplyHint}
			<Katex math={math.solLhs + " = " + math.solRhs} displayMode/>
		{/if}
		{#if !isApplyHint}
			<button on:click={applyHint}>Apply Hint</button><br>
		{/if}
	{/if}
	<button class="next-button" class:press-next="{pressNext}" on:click={newQuestion}>Next question</button>
</div>

<style>
.flex-container {
    display: flex;
    flex-direction: column;
    width: 30em;
	flex-grow: 1;
}

.flex-container button {
    flex: 1;
}

.next-button {
  background: linear-gradient(to right, #646cff 50%, #1a1a1a 50%);
  background-size: 200% 100%;
  background-position: right bottom;
  transition: all .5s ease-out;
  background-origin: border-box;
}
.press-next {
  background-position: left bottom;
}

@media (prefers-color-scheme: light) {
    .next-button {
        background: linear-gradient(to right, #646cff 50%, #f9f9f9 50%);
        background-size: 200% 100%;
        background-position: right bottom;
        transition: all .5s ease-out;
        background-origin: border-box;
    }
    .press-next {
        background-position: left bottom;
    }
}
</style>
