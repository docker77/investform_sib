<template>
  <div class="modal-flex">
    <form class="form" @submit.prevent="submitHandler">
      <div class="container">
        <h1>Инвестировать сейчас</h1>
      </div>
      <div class="line"></div>

      <div class="container">
        <div class="formrow">
          <div class="rowcol">
            <label for="sumInv">Сумма инвестиции</label>
          </div>

          <div class="rowcol">
            <div v-if="errorSumInv" v-html="errorSumInv" class="error"></div>
            <div :class="errorSumInv ? 'myInput errorborder' : 'myInput'">
              <span class="inputAddon">$</span>
              <MyNumberInput
                class="inputItem"
                v-model="sumInv"
              />
            </div>
          </div>
        </div>

        <div class="formrow">
          <div class="rowcol">
            <label for="mult">Мультипликатор</label>
          </div>
          <div class="rowcol">
            <div v-if="errorMult" class="error">{{ errorMult }}</div>
            <div
              id="mult"
              :class="errorMult ? 'myInput errorborder' : 'myInput'"
            >
              <span class="inputAddon">x</span>
              <input
                class="inputItem"
                type="text"
                v-model="mult"
                @mouseover="sliderVisible = true"
              />
            </div>
            <div id="finInv">= {{ (mult * sumInv).toLocaleString("ru") }}</div>
          </div>
        </div>

        <div
          v-show="sliderVisible"
          class="slidecontainer"
          @mouseleave="sliderVisible = false"
        >
          <input
            type="range"
            min="1"
            max="40"
            v-model="mult"
            class="slider"
            id="myRange"
          />
          <div class="sliderlabels">
            <span style="left=0">1</span>
            <span style="left:12%">5</span >
            <span style="left:47%">20</span>
            <span style="right:0;">40</span>
          </div>
        </div>
      </div>

      <div class="container pt-1">
        <div class="boundaryswitch" @click="boundarySwitch">
          <span
            :class="isBoundaries ? 'chevron bottom' : 'chevron right'"
          ></span>
          <span>Ограничить прибыль или убыток</span>
        </div>
        <div
          id="boundaries"
          :style="[
            isBoundaries
              ? { visibility: 'visible' }
              : { visibility: 'hidden' },
          ]"
        >
          <div class="formrow pt-1">
            <div class="rowcol">
              <span>Ограничения в</span>
            </div>

            <div class="rowcol">
              <input
                type="radio"
                name="radiobutton"
                value="%"
                v-model="boundaryUnits"
              /><label>%</label>
              <input
                type="radio"
                name="radiobutton"
                value="$"
                v-model="boundaryUnits"
              />
              <label>$</label>
            </div>
          </div>
          
          <div class="formrow">
            <div class="rowcol">
              <input
                type="checkbox"
                v-model="isTakeProfit"
                name="limit"
                id="takeprofit"
              /><label for="takeprofit">Прибыль</label>
            </div>

            <div class="rowcol">
              <div
                :class="errorTakeProfit ? 'myInput errorborder' : 'myInput'"
              >
                <span class="inputAddon">{{ boundaryUnits }}</span>
                <MyNumberInput
                  class="inputItem"
                  v-model="takeProfit"
                  :disabled="!isTakeProfit"
                />
                <InputNumberArrows class="inputAddon" v-model="takeProfit" :disabled="!isTakeProfit"/>
              </div>

              <div v-if="errorTakeProfit" class="error">
                {{ errorTakeProfit }}
              </div>
            </div>
          </div>
          
          <div class="formrow">
            <div class="rowcol">
              <input
                type="checkbox"
                id="stoploss"
                name="limit"
                v-model="isStopLoss"
              /><label for="stoploss">Убыток</label>
            </div>

            <div class="rowcol">
              <div :class="errorStopLoss ? 'myInput errorborder' : 'myInput'">
                <span class="inputAddon">{{ boundaryUnits }}</span>
                <MyNumberInput
                  class="inputItem"
                  v-model="stopLoss"
                  :disabled="!isStopLoss"
                />
                <InputNumberArrows class="inputAddon" v-model="stopLoss" :disabled="!isStopLoss"/>
              </div>
              <div v-if="errorStopLoss" class="error">{{ errorStopLoss }}</div>
            </div>

          </div>
        </div>

        <div class="formrow pt-1">
          <button
            type="submit"
            class="btn btn-sep btn-3 icon-down rawcol"
            @click="direction = 'reduction'"
          >
            В снижение
          </button>
          <button
            type="submit"
            class="btn btn-sep btn-2 icon-up rawcol"
            @click="direction = 'growth'"
          >
            В рост
          </button>
        </div>
      </div>
    </form>
    <div v-html="serverData" style="font-size: 16px;"></div>
  </div>
</template>

<script>
import MyNumberInput from "./MyNumberInput";
import InputNumberArrows from "./InputNumberArrows"

export default {
  name: "Form",
  components: {
    MyNumberInput,
    InputNumberArrows
  },
  data() {
    return {
      isBoundaries: false,
      isTakeProfit: false,
      isStopLoss: false,
      takeProfit: 0,
      stopLoss: 0,
      sumInv: 5000,
      mult: 40,
      direction: "",
      boundaryUnits: "$",
      visible: true,
      errorSumInv: "",
      errorMult: "",
      errorStopLoss: "",
      errorTakeProfit: "",
      serverData: "",
      sliderVisible: false,
    };
  },
  methods: {
    boundarySwitch() {
      this.isBoundaries = !this.isBoundaries;
      this.setProfitLoss();
    },

    setProfitLoss() {
      if (this.boundaryUnits == "$") {
        this.takeProfit = parseInt(this.sumInv * 0.3);
        this.stopLoss = parseInt(this.sumInv * 0.3);
      } else {
        this.takeProfit = 30;
        this.stopLoss = 30;
      }
    },

    errorSumHandler() {
      this.errorSumInv = ""
      if (this.sumInv < 100) {
        this.errorSumInv =
          "Минимальная сумма инвестиции <span class='underdot'>$ 100</span>";
      }
    },

    errorMultHandler() {
      this.errorMult = ""
      if (this.mult < 1 || this.mult > 40) {
        this.errorMult = "Неверное значение мультипликатора";
      }
    },

    errorStopLossHandler() {
      this.errorStopLoss = ""
      if (this.stopLoss > this.sumInv && this.boundaryUnits == "$") {
        this.errorStopLoss = "Не может быть больше $ " + this.sumInv;
      } else if (this.stopLoss > 100 && this.boundaryUnits == "%") {
        this.errorStopLoss = "Не может быть больше 100%";
      }
    },

    errorTakeProfitHandler() {
      this.errorTakeProfit = ""
      if (
        this.takeProfit < parseInt(this.sumInv * 0.1) &&
        this.boundaryUnits == "$"
      ) {
        this.errorTakeProfit =
          "Не может быть меньше $ " + parseInt(this.sumInv * 0.1);
      } else if (this.takeProfit < 10 && this.boundaryUnits == "%") {
        this.errorTakeProfit = "Не может быть меньше 10%";
      }
    },

    formIsValid() {
      if (
        this.errorTakeProfit ||
        this.errorStopLoss ||
        this.errorMult ||
        this.errorSumInv
      ) {
        return false;
      }
      return true;
    },
    submitHandler() {
      if (!this.formIsValid()) {
        alert("Пожалуйста, исправьте все ошибки!");
        return;
      }

      this.serverData = "<h1>Submitted</h1>";
      this.serverData += "Sum: " + this.sumInv + "<br />";
      this.serverData += "Direction: " + this.direction + "<br />";
      this.serverData += "Multiplier: " + this.mult + "<br />";
      if (this.isTakeProfit && this.isBoundaries)
        this.serverData +=
          "takeProfit: " +
          (this.boundaryUnits == "$"
            ? this.takeProfit
            : parseInt((this.takeProfit * this.sumInv) / 100)) +
          "<br />";
      if (this.isStopLoss && this.isBoundaries)
        this.serverData +=
          "stopLoss: " +
          (this.boundaryUnits == "$"
            ? this.stopLoss
            : parseInt((this.stopLoss * this.sumInv) / 100));
      console.log(this.serverData);
    },
  },
  watch: {
    sumInv() {
      this.setProfitLoss();
      this.errorSumHandler()
    },
    boundaryUnits() {
      this.setProfitLoss()
    },
    takeProfit() {
      this.errorTakeProfitHandler()
    },
    stopLoss() {
      this.errorStopLossHandler()
    },
    mult() {
      this.errorMultHandler()
    },
    isTakeProfit(state) {
      if (state) { this.errorTakeProfitHandler() }
        else { this.errorTakeProfit = '' }
    },
    isStopLoss(state) {
      if (state) { this.errorStopLossHandler() }
        else { this.errorStopLoss = '' }
    }
  },
};
</script>

<style scoped>
.pt-1 {
  padding-top: 1rem;
}
.modal-flex {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.formrow {
  display: flex;
  align-items: center;
  justify-content: left;
  margin: 10px 0;
}

.rowcol {
  display: flex;
  position: relative;
  align-items: center;
  width: 50%;
}

.myInput {
  display: flex;
  width: 100%;
  align-items: center;
}

.myInput.errorborder > .inputItem,
.myInput.errorborder > .inputAddon {
  border: 1px solid #e64545;
}

.inputItem {
  outline: none;
  width: 100%;
  font-size: 12px;
  border-left: none;
  text-align: right;
}

.inputAddon {
  flex: 1;
  font-size: 12px;
  color: lightgray;
  border-right: none;
}

.inputAddon:not(:first-child),
.inputItem:not(:first-child) {
  border-left: 0 !important;
}
.inputAddon:not(:last-child),
.inputItem:not(:last-child) {
  border-right: 0 !important;
}

.inputAddon,
.inputItem {
  border: 1px solid lightgray;
  padding: 0.5em 0.75em;
}

.inputAddon.error,
.inputItem.error {
  border: 1px solid #e64545;
}

.inputItem:first-child,
.inputAddon:first-child {
  border-radius: 3px 0 0 3px;
}
.inputItem:last-child,
.inputAddon:last-child {
  border-radius: 0 3px 3px 0;
}

input[type="checkbox"],
input[type="radio"] {
  cursor: pointer;
}
.form {
  width: 350px;
  position: relative;
  background-color: white;
  margin: 50px 0;
}

.container {
  padding: 10px 20px;
}

.line {
  width: 100%;
  height: 1px;
  background-color: #e0e2e4;
}

.error {
  color: white;
  position: absolute;
  top: -2.9rem;
  padding: 5px;
  border: 1px solid #e64545;
  background-color: #e64545;
  border-radius: 3px;
}

.error:after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 10%;
  width: 0;
  height: 0;
  border: 5px solid transparent;
  border-top-color: #e64545;
  border-bottom: 0;
  margin-left: -5px;
  margin-bottom: -5px;
}

.error.underdot {
  border-bottom: 1px dotted white;
}

#mult {
  width: 50%;
  border-radius: 0 3px 3px 0;
  margin-right: 5px;
}

#finInv {
  color: lightgrey;
}

.boundaryswitch {
  color: #909294;
  display: flex;
  justify-content: left;
  align-items: center;
  cursor: pointer;
}

.chevron {
  margin-right: 0.4rem;
}
.chevron::before {
  border-style: solid;
  border-width: 0.15em 0.15em 0 0;
  content: "";
  display: inline-block;
  height: 0.35em;
  left: 0.15em;
  position: relative;
  top: 0.2em;
  transform: rotate(-45deg);
  vertical-align: top;
  width: 0.35em;
}

.chevron.right:before {
  left: 0;
  transform: rotate(45deg);
}

.chevron.bottom:before {
  top: 0;
  transform: rotate(135deg);
}

.chevron.left:before {
  left: 0.25em;
  transform: rotate(-135deg);
}

.primary {
  color: #42b983;
}

.danger {
  color: #ff8080;
}

.container {
  margin: 0 auto;
  max-width: 1000px;
}

/* General button style */
.btn {
  width: 50%;
  border: none;
  font-size: inherit;
  color: inherit;
  background: none;
  cursor: pointer;
  padding: 5px 40px 5px 80px;
  display: inline-block;
  margin: 5px 5px;
  font-weight: 700;
  outline: none;
  position: relative;
  -webkit-transition: all 0.3s;
  -moz-transition: all 0.3s;
  transition: all 0.3s;
  border-radius: 3px;
}

.btn:after {
  content: "";
  position: absolute;
  z-index: -1;
  -webkit-transition: all 0.3s;
  -moz-transition: all 0.3s;
  transition: all 0.3s;
}

/* Pseudo elements for icons */
.btn:before {
  speak: none;
  font-style: normal;
  font-weight: normal;
  font-variant: normal;
  text-transform: none;
  line-height: 1;
  position: relative;
  -webkit-font-smoothing: antialiased;
}

/* Icon separator */
.btn-sep {
  padding: 15px 20px 15px 50px;
}

.btn-sep:before {
  background: rgba(0, 0, 0, 0.15);
}

/* Button 2 */
.btn-2 {
  background: #2ecc71;
  color: #fff;
}

.btn-2:hover {
  background: #27ae60;
}

.btn-2:active {
  background: #27ae60;
  top: 2px;
}

.btn-2:before {
  position: absolute;
  height: 100%;
  left: 0;
  top: 0;
  line-height: 3;
  font-size: 140%;
  width: 60px;
}

/* Button 3 */
.btn-3 {
  background: #f26666;
  color: #fff;
}

.btn-3:hover {
  background: #c0392b;
}

.btn-3:active {
  background: #c0392b;
  top: 2px;
}

.btn-3:before {
  position: absolute;
  height: 100%;
  left: 0;
  top: 0;
  line-height: 3;
  font-size: 140%;
  width: 60px;
}

/* Icons */

.icon-up:before {
  content: url("data:image/svg+xml; utf8, %3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 20 20' fill='lightgray'%3E%3Cpath fill-rule='evenodd' d='M3.293 9.707a1 1 0 010-1.414l6-6a1 1 0 011.414 0l6 6a1 1 0 01-1.414 1.414L11 5.414V17a1 1 0 11-2 0V5.414L4.707 9.707a1 1 0 01-1.414 0z' clip-rule='evenodd' /%3E%3C/svg%3E");
  width: 15px;
  padding: 0 10px;
}

.icon-down:before {
  content: url("data:image/svg+xml; utf8, %3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 20 20' fill='lightgray'%3E%3Cpath fill-rule='evenodd' d='M16.707 10.293a1 1 0 010 1.414l-6 6a1 1 0 01-1.414 0l-6-6a1 1 0 111.414-1.414L9 14.586V3a1 1 0 012 0v11.586l4.293-4.293a1 1 0 011.414 0z' clip-rule='evenodd' /%3E%3C/svg%3E");
  width: 15px;
  padding: 0 10px;
}

.slidecontainer {
  position: absolute;
  background: #303030;
  border-radius: 2px;
  width: 90%;
  padding: 15px 10px 40px 10px;
  z-index: 100;
}

.slidecontainer:after {
  content: "";
  position: absolute;
  top: 0;
  left: 62%;
  width: 0;
  height: 0;
  border: 5px solid transparent;
  border-bottom-color: #303030;
  border-top: 0;
  margin-left: -5px;
  margin-top: -5px;
}

.slider {
  -webkit-appearance: none;
  width: 100%;
  height: 5px;
  border-radius: 2px;
  background: #7e98e5;
  outline: none;
  opacity: 0.7;
  -webkit-transition: 0.2s;
  transition: opacity 0.2s;
}

.slider:hover {
  opacity: 1;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 17px;
  height: 17px;
  border-radius: 50%;
  border: 6px solid #7e98e5;
  background: white;
  cursor: pointer;
}

.slider::-moz-range-thumb {
  width: 15px;
  height: 15px;
  border-radius: 50%;
  background: #7e98e5;
  cursor: pointer;
}

.sliderlabels { position: relative; }

.sliderlabels span {
  display:block; 
  position: absolute;
  top: 10px;
  color: gray;
}
</style>
