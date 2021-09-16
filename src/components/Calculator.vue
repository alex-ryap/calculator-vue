<template>
  <div class="container">
    <div class="calculator">
      <div class="result">
        <span class="result__text">{{ currentNum || '0' }}</span>
      </div>
      <button class="btn btn-suboperation" @click="clear">AC</button>
      <button class="btn btn-suboperation" @click="changeSign">
        <sup>+</sup>/-
      </button>
      <button class="btn btn-suboperation" @click="setOperation($event)">
        x<sup>y</sup>
      </button>
      <button class="btn btn-operation" @click="setOperation($event)">/</button>
      <button class="btn" @click="setNumber('7')">7</button>
      <button class="btn" @click="setNumber('8')">8</button>
      <button class="btn" @click="setNumber('9')">9</button>
      <button class="btn btn-operation" @click="setOperation($event)">x</button>
      <button class="btn" @click="setNumber('4')">4</button>
      <button class="btn" @click="setNumber('5')">5</button>
      <button class="btn" @click="setNumber('6')">6</button>
      <button class="btn btn-operation" @click="setOperation($event)">-</button>
      <button class="btn" @click="setNumber('1')">1</button>
      <button class="btn" @click="setNumber('2')">2</button>
      <button class="btn" @click="setNumber('3')">3</button>
      <button class="btn btn-operation" @click="setOperation($event)">+</button>
      <button class="btn btn-medium" @click="setNumber('0')">0</button>
      <button class="btn" @click="convertToFloat">.</button>
      <button class="btn btn-operation" @click="calculate">=</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Calculator',

  data: () => ({
    currentNum: '',
    previousNum: null,
    operation: null,
    selectedOperation: false,
  }),

  methods: {
    // reset all
    clear() {
      this.removeSelected();
      this.currentNum = '';
      this.previousNum = this.operation = null;
      this.selectedOperation = false;
    },

    // set current selected number to (this.num1 or this.num2)
    setNumber(number) {
      if (this.selectedOperation) {
        this.removeSelected();
        this.selectedOperation = false;
        this.currentNum = number;
      } else {
        this.currentNum =
          this.currentNum === '0' ? number : this.currentNum + number;
      }
    },

    // change sign of number
    changeSign() {
      this.currentNum =
        this.currentNum.charAt(0) === '-'
          ? this.currentNum.slice(1)
          : '-' + this.currentNum;
    },

    // convert integer number to float
    convertToFloat() {
      if (this.selectedOperation) {
        this.currentNum = '0.';
        this.removeSelected();
        this.selectedOperation = false;
      }
      if (this.currentNum.indexOf('.') === -1) {
        this.currentNum = this.currentNum === '' ? '0.' : this.currentNum + '.';
      }
    },

    // set current selected operation to this.operation
    setOperation(event) {
      this.removeSelected();
      this.selectedOperation = true;
      const numberValue = parseFloat(this.currentNum);

      if (this.previousNum == null) {
        this.previousNum = numberValue;
      } else if (this.operation) {
        this.calculate();
        this.previousNum = this.currentNum;
      }
      this.operation = event.currentTarget;
      this.operation.classList.add('btn-selected');
    },

    // calculates result depending on the operation
    calculate() {
      let { previousNum, currentNum, operation } = this;
      let result = null;
      switch (operation.innerText) {
        case '+': {
          result = previousNum + +currentNum;
          break;
        }
        case '-': {
          result = previousNum - currentNum;
          break;
        }
        case '/': {
          result = currentNum == 0 ? 'Error' : previousNum / currentNum;
          break;
        }
        case 'x': {
          result = previousNum * currentNum;
          break;
        }
        case 'xy': {
          result = Math.pow(previousNum, currentNum);
          break;
        }
      }
      if (result.toString().length > 10) result = result.toPrecision(6);

      this.currentNum = result.toString();
      this.previousNum = null;
    },

    // remove selected class from operation button
    removeSelected() {
      if (this.selectedOperation) {
        this.selectedOperation = false;
        this.operation.classList.remove('btn-selected');
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: center;
  min-height: 100vh;
  padding: 30px 20px 15px;
}

.calculator {
  min-width: 300px;
  max-width: 500px;
  width: 100%;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 15px;
}

.result {
  height: 100px;
  grid-column: 1 / 5;
  display: flex;
  align-items: flex-end;
  justify-content: flex-end;

  &__text {
    font-size: 5rem;
    color: #ffffff;
  }
}

.btn {
  cursor: pointer;
  outline: none;
  padding: 1.5rem;
  border-radius: 20px;
  font-size: 2rem;
  border: none;
  background-color: #333333;
  color: #ffffff;

  &:hover {
    opacity: 0.7;
  }

  &:active {
    opacity: 0.5;
  }

  &-medium {
    grid-column: 1 / 3;
  }

  &-big {
    grid-column: 1 / 4;
  }

  &-suboperation {
    color: #000000;
    background-color: #a5a6a5;
  }

  &-operation {
    background-color: #ff9f0a;
  }

  &-selected {
    background-color: #ffffff;
    color: #ff9f0a;
  }
}

@media screen and (max-width: 600px) {
  .container {
    min-height: 90vh;
  }
  .btn {
    font-size: 20px;
  }
}
</style>
