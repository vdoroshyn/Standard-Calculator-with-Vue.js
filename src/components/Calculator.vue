<template>
  <div class=calculator>
    <div class="display">{{current || 0}}</div>
    <div @click="clear"          class="btn">AC</div>
    <div @click="changeSign"     class="btn">+/-</div>
    <div @click="percent"        class="btn">%</div>
    <div @click="division"       class="btn operator">/</div>
    <div @click="append('7')"    class="btn">7</div>
    <div @click="append('8')"    class="btn">8</div>
    <div @click="append('9')"    class="btn">9</div>
    <div @click="multiplication" class="btn operator">x</div>
    <div @click="append('4')"    class="btn">4</div>
    <div @click="append('5')"    class="btn">5</div>
    <div @click="append('6')"    class="btn">6</div>
    <div @click="substraction"   class="btn operator">-</div>
    <div @click="append('1')"    class="btn">1</div>
    <div @click="append('2')"    class="btn">2</div>
    <div @click="append('3')"    class="btn">3</div>
    <div @click="addition"       class="btn operator">+</div>
    <div @click="append('0')"    class="btn zero">0</div>
    <div @click="dot"            class="btn">.</div>
    <div @click="equals"         class="btn operator">=</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      previous: null,
      current: "0",
      operator: null,
      operatorClicked: false,
      doWeHaveResult: false
    };
  },
  methods: {
    clear() {
      this.current = "0";
      this.previous = null;
      this.operator = null;
      this.operatorClicked = false;
    },
    changeSign() {
      this.current =
        this.current.charAt(0) === "-"
          ? this.current.slice(1)
          : `-${this.current}`;
    },
    percent() {
      this.current = `${parseFloat(this.current) / 100}`;
    },
    deleteLeadingZeroes(number) {
      let isNegative = false;

      if (number.charAt(0) === "-") {
        isNegative = true;
        number = number.slice(1);
      }

      if (
        number.charAt(0) === "0" &&
        number.length > 1 &&
        number.charAt(1) !== "."
      ) {
        number = number.slice(1);
      }

      return isNegative === true ? `${"-"}${number}` : number;
    },
    checksBeforeAppend() {
      if (this.operatorClicked === true) {
        this.previous = this.current;
        this.current = "0";
        this.operatorClicked = false;
      } else if (this.doWeHaveResult && !this.operator) {
        this.previous = null;
        this.current = "0";
        this.doWeHaveResult = false;
      }
    },
    append(number) {
      this.checksBeforeAppend();

      this.current = `${this.current}${number}`;
      this.current = this.deleteLeadingZeroes(this.current);
    },
    dot() {
      if (this.current.indexOf(".") === -1) {
        this.append(".");
      }
    },
    isMathDoneWithoutEquals() {
      /*
      **added this line here not to repeat the same line in 4 places
      */
      this.operatorClicked = true;

      if (this.operator) {
        this.equals();
        return true;
      }

      return false;
    },
    division() {
      this.isMathDoneWithoutEquals();
      this.operator = (a, b) => a / b;
    },
    multiplication() {
      this.isMathDoneWithoutEquals();
      this.operator = (a, b) => a * b;
    },
    substraction() {
      this.isMathDoneWithoutEquals();
      this.operator = (a, b) => a - b;
    },
    addition() {
      this.isMathDoneWithoutEquals();
      this.operator = (a, b) => a + b;
    },
    equals() {
      if (!this.previous || !this.current) {
        return;
      }
      /*
      **The number of string-number and vice versa casts is too big here
      **However, it was nevessary in order to fix the error of calculations
      */
      this.current = (+this.operator(
        parseFloat(this.previous),
        parseFloat(this.current)
      ).toFixed(10)).toString();

      this.previous = null;
      this.operator = null;
      this.doWeHaveResult = true;
    }
  }
};
</script>

<style scoped>
.calculator {
  margin: 0 auto;
  width: 300px;
  font-size: 40px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: minmax(50px, auto);
}

.display {
  grid-column: 1 / 5;
  background-color: #333;
  color: white;
  overflow: auto;
}

.btn {
  background-color: #f2f2f2;
  border: 1px solid #999;
  cursor: pointer;
}

.btn:active {
  box-shadow: 0 2px #666;
  transform: translateY(2px);
}

.operator {
  background-color: orange;
  color: white;
}

.zero {
  grid-column: 1 / 3;
}
</style>
