---
layout: post
title: Javascript Calculator
courses: { compsci: {week: 4} }
type: hacks
toc: true
comments: false
---

<!-- 
Hack 0: Right justify the result
Hack 1: Test conditions on small, big, and decimal numbers, and report on findings. Fix issues.
Hack 2: Add the common math operation that is missing from the calculator
Hack 3: Implement 1 number operation (ie SQRT) 
-->

<!-- 
HTML implementation of the calculator. 
-->

<!-- 
    Style and Action are aligned with HRML class definitions
    style.css contains the majority of style definitions (number, operation, clear, and equals)
    - The div calculator container sets 4 elements to a row
    The background is credited to Vanta JS and is implemented at the bottom of this page
-->
<style>
  * {
    font-family: monospace; /* Or any font of your choice */
  }
  .calc-button,.calculator-number,.calculator-operation {
    width: auto;
    height: auto;
    border-radius: 10px;
    background-color: #aaefdf;
    border: 3px solid #000;
    font-size: 1.5em;
    display: flex;
    justify-content: center;
    align-items: center;
    grid-column: span 1;
    grid-row: span 1;
    transition: all 0.5s
}
.calc-button:hover,.calculator-number:hover,.calculator-operation:hover {
    background-color: #483D8B
}
.calculator-container {
    width: 90vw;
    height: 80vh;
    margin: 0 auto;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: 0.5fr repeat(4, 1fr);
    gap: 10px 10px
}
@media (min-width: 600px) {
    .calculator-container {
        width:40vw;
        height: 80vh
    }
}
.calculator-clear {
    width: auto;
    height: auto;
    border-radius: 10px;
    background-color: #db995a;
    border: 3px solid #000;
    font-size: 1.5em;
    display: flex;
    justify-content: center;
    align-items: center;
    grid-column: span 1;
    grid-row: span 1;
    transition: all 0.5s
}
.calculator-clear:hover {
    background-color: #FF8C00
}
.calculator-equals {
    width: auto;
    height: auto;
    border-radius: 10px;
    background-color: #8c1c13;
    border: 3px solid #000;
    font-size: 1.5em;
    display: flex;
    justify-content: center;
    align-items: center;
    grid-column: span 1;
    grid-row: span 1;
    transition: all 0.5s
}
.calculator-equals:hover {
    background-color: red
}
  .calculator-output {
    /*
      calculator output
      the top bar shows the results of the calculator;
      result to take up the entirety of the first row;
      span defines 4 columns and 1 row
    */
    grid-column: span 4;
    grid-row: span 1;
    border-radius: 10px;
    padding: 0.25em;
    font-size: 20px;
    border: 5px solid black;
    display: flex;
    align-items: center;
    color: #ffffff;
    
  }
  canvas {
    filter: none;
  }
  .calculator-output {
    /*
      calculator output
      the top bar shows the results of the calculator;
      result to take up the entirety of the first row;
      span defines 4 columns and 1 row
    */
    grid-column: span 4;
    grid-row: span 1;
  
    border-radius: 10px;
    padding: 0.25em;
    font-size: 20px;
    border: 5px solid black;
  
    display: flex;
    align-items: center;
  }
  canvas {
    filter: none;
  }
</style>

<!-- Add a container for the animation -->
<div id="animation">
  <div class="calculator-container">
      <!--result-->
      <div class="calculator-output" id="output">0</div>
      <!--row 1-->
      <div class="calculator-number">1</div>
      <div class="calculator-number">2</div>
      <div class="calculator-number">3</div>
      <div class="calculator-operation">+</div>
      <!--row 2-->
      <div class="calculator-number">4</div>
      <div class="calculator-number">5</div>
      <div class="calculator-number">6</div>
      <div class="calculator-operation">-</div>
      <!--row 3-->
      <div class="calculator-number">7</div>
      <div class="calculator-number">8</div>
      <div class="calculator-number">9</div>
      <div class="calculator-operation">*</div>
      <!--row 4-->
      <div class="calculator-clear">A/C</div>
      <div class="calculator-number">0</div>
      <div class="calculator-number">.</div>
      <div class="calculator-equals">=</div>
  </div>
</div>

<!-- JavaScript (JS) implementation of the calculator. -->
<script>
// initialize important variables to manage calculations
var firstNumber = null;
var operator = null;
var nextReady = true;
//Build objects containing key elements
const output = document.getElementById("output");
const numbers = document.querySelectorAll(".calculator-number");
const operations = document.querySelectorAll(".calculator-operation");
const clear = document.querySelectorAll(".calculator-clear");
const equals = document.querySelectorAll(".calculator-equals");

// Number buttons listener
numbers.forEach(button => {
  button.addEventListener("click", function() {
    number(button.textContent);
  });
});

// Number action
function number (value) { // function to input numbers into the calculator
    if (value != ".") {
        if (nextReady == true) { // nextReady is used to tell the computer when the user is going to input a completely new number
            output.innerHTML = value;
            if (value != "0") { // if statement to ensure that there are no multiple leading zeroes
                nextReady = false;
            }
        } else {
            output.innerHTML = output.innerHTML + value; // concatenation is used to add the numbers to the end of the input
        }
    } else { // special case for adding a decimal; can't have two decimals
        if (output.innerHTML.indexOf(".") == -1) {
            output.innerHTML = output.innerHTML + value;
            nextReady = false;
        }
    }
}

// Operation buttons listener
operations.forEach(button => {
  button.addEventListener("click", function() {
    operation(button.textContent);
  });
});

// Operator action
function operation (choice) { // function to input operations into the calculator
    if (firstNumber == null) { // once the operation is chosen, the displayed number is stored into the variable firstNumber
        firstNumber = parseInt(output.innerHTML);
        nextReady = true;
        operator = choice;
        return; // exits function
    }
    // occurs if there is already a number stored in the calculator
    firstNumber = calculate(firstNumber, parseFloat(output.innerHTML)); 
    operator = choice;
    output.innerHTML = firstNumber.toString();
    nextReady = true;
}

// Calculator
function calculate (first, second) { // function to calculate the result of the equation
    let result = 0;
    switch (operator) {
        case "+":
            result = first + second;
            break;
        case "-":
            result = first - second;
            break;
        case "*":
            result = first * second;
            break;
        case "/":
            result = first / second;
            break;
        default: 
            break;
    }
    return result;
}

// Equals button listener
equals.forEach(button => {
  button.addEventListener("click", function() {
    equal();
  });
});

// Equal action
function equal () { // function used when the equals button is clicked; calculates equation and displays it
    firstNumber = calculate(firstNumber, parseFloat(output.innerHTML));
    output.innerHTML = firstNumber.toString();
    nextReady = true;
}

// Clear button listener
clear.forEach(button => {
  button.addEventListener("click", function() {
    clearCalc();
  });
});

// A/C action
function clearCalc () { // clears calculator
    firstNumber = null;
    output.innerHTML = "0";
    nextReady = true;
}
</script>

<!-- 
Vanta animations just for fun, load JS onto the page
-->
<script src="{{site.baseurl}}/assets/js/calculator/three.r119.min.js"></script>
<script src="{{site.baseurl}}/assets/js/calculator/vanta.halo.min.js"></script>
<script src="{{site.baseurl}}/assets/js/calculator/vanta.birds.min.js"></script>
<script src="{{site.baseurl}}/assets/js/calculator/vanta.net.min.js"></script>
<script src="{{site.baseurl}}/assets/js/calculator/vanta.rings.min.js"></script>

<script>
// setup vanta scripts as functions
var vantaInstances = {
  halo: VANTA.HALO,
  birds: VANTA.BIRDS,
  net: VANTA.NET,
  rings: VANTA.RINGS
};

// obtain a random vanta function
var vantaInstance = vantaInstances[Object.keys(vantaInstances)[Math.floor(Math.random() * Object.keys(vantaInstances).length)]];

//Run the animation
vantaInstance({
  el: "#animation",
  mouseControls: true,
  touchControls: true,
  gyroControls: false
});
</script>


