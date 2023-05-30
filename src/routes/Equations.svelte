<script lang="ts">
import Katex from "../Katex.svelte";

let symbols = ["a", "x", "m", "b", "y", "n", "c", "d"];
let operations = ["sum", "diff", "div", "prod"];

function rand(max: number) : number {
    return Math.floor(Math.random() * max);
}

function delay(seconds) {
  return new Promise(resolve => setTimeout(resolve, seconds * 1000));
}

function generateEquation() {
    let lhs = symbols[rand(symbols.length)];
    let num =  rand(15) + 1;
    let rhs = (rand(2) == 1) ? num : -num;

    let steps = [`\\green{${lhs}} &\\green{= ${rhs}}`];
    let solution = rhs;
    let equation = "";

    let last_op = "";
    for(let j = 0; j < 3; j += 1) {
        let op = operations[rand(operations.length)];
        while(last_op === op || (Math.abs(rhs) <= 1 && op === "div")) {
            op = operations[rand(operations.length)];
        }
        if (op == "sum") {
            let num = rand(30) + 1;
            lhs = `${lhs} + ${num}`;
            rhs += num;
            steps.push(`${lhs} = ${rhs} \\quad| - ${num}`);
        } else if (op == "diff") {
            let num = rand(30) + 1;
            lhs = `${lhs} - ${num}`;
            rhs -= num;
            steps.push(`${lhs} = ${rhs} \\quad| + ${num}`);
        } else if (op == "div") {
            let divs = [rhs, -rhs];
            for(let i = 2; i * i <= Math.abs(rhs); ++i) {
                if(rhs % i == 0) {
                    divs.push(i);
                    divs.push(-i);
                    divs.push(rhs/i);
                    divs.push(-rhs/i);
                }
            }
            let num = divs[rand(divs.length)];
            lhs = `\\frac{${lhs}}{${num}}`;
            rhs /= num;
            steps.push(
                num >= 0 ?
                `${lhs} = ${rhs} \\quad| \\cdot ${num}`
                : `${lhs} = ${rhs} \\quad| \\cdot (${num})`
            );
        } else if (op == "prod") {
            let num = rand(10) + 1;
            num = (rand(2) == 0) ? num : -num;
            while(num == 1) {
                num = rand(10) + 1;
                num = (rand(2) == 0) ? num : -num;
            }
            if (last_op == "sum" || last_op == "diff") {
                lhs = 
                    num >= 0 ?
                    `\\left(${lhs}\\right) \\cdot ${num}`
                    : `\\left(${lhs}\\right) \\cdot (${num})`;
            } else {
                lhs =
                    num >= 0 ?
                    `${lhs} \\cdot ${num}`
                    : `${lhs} \\cdot (${num})`;
            }
            rhs *= num;
            steps.push(
                num >= 0 ?
                `${lhs} = ${rhs} \\quad| : ${num}`
                : `${lhs} = ${rhs} \\quad| : (${num})`
            );
        }
        equation = `${lhs} = ${rhs}`;
        last_op = op;
    }

    steps = steps.reverse();
    return {steps: steps, equation: equation, solution: solution};
}

let problem = generateEquation();
let stepIndex = 0;
$: eq = "\\begin{align*}\n" + problem.steps.slice(0, stepIndex).join("\\\\ ").replaceAll(" = ", " &= ") + "\\end{align*}"

let showAnswer = false;
let guessAnswer;
let wrongCheck = false;
let correctCheck = false;
let pressNext = false;
function checkAnswer() {
    if (guessAnswer == problem.solution) {
        correctCheck = true;
        pressNext = true;
        delay(1).then(() => correctCheck = false);
    } else {
        wrongCheck = true;
        delay(1).then(() => wrongCheck = false);
    }
}
function showEntireSolution() {
    showAnswer = true;
    recursiveStepInc();
}
function recursiveStepInc() {
    if(stepIndex < problem.steps.length) {
        delay(0.1).then(() => {
            stepIndex += 1
            if(stepIndex < problem.steps.length) {
                recursiveStepInc();
            }
        });
    }
}
function nextQuestion() {
    showAnswer = false;
    stepIndex = 0;
    problem = generateEquation();
    guessAnswer = "";
    pressNext = false;
}

$: if(stepIndex >= problem.steps.length) {
    pressNext = true;
}

</script>

<div class="flex-container">
    <Katex math={problem.equation} displayMode />

    <div class="answer row">
        <div class="answer-input"><span>Answer:</span> <input type=number autocomplete=off size="5" bind:value={guessAnswer}></div>
        <button on:click={checkAnswer} class:wrongCheck="{wrongCheck}" class:correctCheck="{correctCheck}">Check Answer</button><br>
    </div>

    {#if showAnswer && stepIndex > 0}
        <Katex math={eq} displayMode />
    {/if}
    <div class="solution row">
        <button on:click={showEntireSolution}>
            Show Entire Solution
        </button>

        <button on:click={() => {
            showAnswer = true; 
            stepIndex += 1;
        }}>
            Hint
        </button><br>
    </div>

    <div class="next-question row">
    <button class="next-button" class:press-next="{pressNext}" on:click={nextQuestion}>Next Question</button>
    </div>
</div>

<style>
.flex-container {
    display: flex;
    flex-direction: column;
}

.flex-container > div {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    margin-bottom: 10px;
}

.flex-container > div:first-child {
    margin-bottom: 20px;
}

.flex-container > div:last-child {
    margin-bottom: 0;
}

.flex-container .answer-input {
    flex: 1;
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


.correctCheck {
  animation: squish 1s cubic-bezier(.36,.07,.19,.97) both, colorChangeGreen 1s ease-in-out;
  transform: scale(1);
  backface-visibility: hidden;
  perspective: 1000px;
}

.wrongCheck {
  animation: shake 1s cubic-bezier(.36,.07,.19,.97) both, colorChangeRed 1s ease-in-out;
  transform: translate3d(0, 0, 0);
  backface-visibility: hidden;
  perspective: 1000px;
}

@keyframes shake {
  10%, 90% {
    transform: translate3d(-2px, 0, 0);
  }
  
  20%, 80% {
    transform: translate3d(3px, 0, 0);
  }

  30%, 50%, 70% {
    transform: translate3d(-5px, 0, 0);
  }

  40%, 60% {
    transform: translate3d(5px, 0, 0);
  }
}

@keyframes squish {
  10%, 90% {
    transform: scale(1.05);
  }
  
  20%, 80% {
    transform: scale(0.95);
  }

  30%, 50%, 70% {
    transform: scale(1.1);
  }

  40%, 60% {
    transform: scale(0.9);
  }
}

@keyframes colorChangeRed {
  50% { background-color: #ff6961; }
}

@keyframes colorChangeGreen {
  50% { background-color: #77dd77; }
}
</style>
