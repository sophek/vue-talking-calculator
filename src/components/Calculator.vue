<template>
  <div class="columns calculator">
    <div class="column">
      <div class="columns">
        <div class="column" style="padding-bottom:20px">
          <h2>Vue Talking Calculator</h2>
        </div>
      </div>
      <div class="columns">
        <div class="column">
          <h1 class="display">{{display}}</h1>
        </div>
      </div>
      <div class="columns">
        <div class="column">
          <div class="keypad">
            <b-button
              :key="'key-' + k"
              v-for="k in keys"
              :type="operatorString(k) === '' ? 'is-primary' : 'is-warning'"
              @click="operatorString(k) === '' ? append(k) : functionFactory(k)"
            >{{k}}</b-button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import formatNumber from "accounting-js/lib/formatNumber.js";

export default {
  name: "Calculator",
  data() {
    return {
      previous: null,
      display: 0,
      operator: null,
      operatorClicked: false,
      decimalCount: 0,
      keys: [
        "AC",
        "+/-",
        "%",
        "÷",
        7,
        8,
        9,
        "x",
        4,
        5,
        6,
        "-",
        1,
        2,
        3,
        "+",
        0,
        ".",
        "Del",
        "="
      ]
    };
  },
  methods: {
    //TextToSpeech.talk("Hello Beautiful World!");

    sign() {
      this.display =
        this.display < 0
          ? (this.display = this.display - this.display * 2)
          : (this.display = this.display - this.display * 2);
    },

    percent() {
      this.display = this.display / 100;
    },

    append(number) {
      //this.sayIt(number);
      console.log(number);

      //Check if there is a decimal, if so increase the decimal count
      if (this.display.toString().indexOf(".") > -1) {
        this.decimalCount += 1;
      }
      //If there is a decimal and the next number is a decimal just return true
      if (this.decimalCount > 1 && number === ".") {
        return;
      }

      if (this.operatorClicked === true) {
        this.display = "";
        this.operatorClicked = false;
      }
      this.display =
        this.display === 0
          ? (this.display = number)
          : "" + this.display + number;
      console.log("operatorClicked", this.operatorClicked);
    },

    functionFactory(func) {
      if (func !== "AC") {
        this.sayIt(formatNumber(this.display));
      }

      let operatorAction = "";
      switch (func) {
        case "AC":
          operatorAction = this.clear();
          break;
        case "+":
          operatorAction = this.add();
          this.sayIt("plus");
          break;
        case "x":
          operatorAction = this.multiply();
          this.sayIt("times");
          break;
        case "÷":
          operatorAction = this.divide();
          this.sayIt("divided by");
          break;
        case "-":
          operatorAction = this.subtract();
          this.sayIt("subtract");
          break;
        case "=":
          this.sayIt("equals");
          operatorAction = this.equal();
          break;
        case "Del":
          operatorAction = this.back();
          break;
        case "%":
          this.sayIt("percent");
          operatorAction = this.percent();
          break;
        case "+/-":
          operatorAction = this.sign();
          break;
        default:
          operatorAction = "";
      }
      if (operatorAction !== "") {
        this.decimalCount = 0;
      }
      return operatorAction;
    },

    decimal() {
      if (this.display.indexOf(".") === -1) {
        this.append(".");
      }
    },

    divide() {
      this.operator = (a, b) => a / b;
      this.previous = this.display;
      this.operatorClicked = true;
    },

    back() {
      this.display = this.display.slice(0, -1);
    },

    multiply() {
      this.operator = (a, b) => a * b;
      this.previous = this.display;
      this.operatorClicked = true;
    },

    subtract() {
      this.operator = (a, b) => a - b;
      this.previous = this.display;
      this.operatorClicked = true;
    },

    add() {
      console.log("add");
      this.operator = (a, b) => a + b;
      this.previous = this.display;
      this.operatorClicked = true;
    },

    equal() {
      console.log("equal");
      this.display = this.operator(Number(this.previous), Number(this.display));
      this.sayIt(formatNumber(this.display));
      this.previous = null;
      this.operatorClicked = true;
    },

    clear() {
      this.this.display = "";
    },
    operatorString(operator) {
      if (operator === ".") {
        return "";
      }
      return isNaN(operator) ? operator : "";
    },
    sayIt(phrase) {
      if ("speechSynthesis" in window) {
        var msg = new SpeechSynthesisUtterance(phrase.replace(".00", ""));
        window.speechSynthesis.speak(msg);
      }
    }
  },
  props: {
    msg: String
  }
};
</script>

<style scoped>
.keypad {
  display: grid;
  grid-template-columns: repeat(4, 5em);
  grid-template-rows: repeat(5, 3em);
  grid-gap: 0em;
}

button {
  margin: 0;
  border-bottom: solid 1px black;
  border-right: solid 1px black;
  border-top: none;
  border-left: none;
  border-radius: 0px;
  height: 2.98em;
  border-color: #000;
}

.button.is-warning,
.button.is-primary {
  border-color: #000;
}

.calculator {
  margin: 50px auto;
  border: solid 1px;
  width: 350px;
}
.display {
  width: 320px;
  margin-top: -27px;
  position: absolute;
  font-size: 1.5rem;
  border: solid 1px #000;
  height: 50px;
  padding: 10px;
}

h2 {
  font-size: 1.5rem;
}
</style>
