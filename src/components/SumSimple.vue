<template>
    <div class="container">
        <div class="row">
            <div class="col-6 task-view">{{ numFirst }} {{ sign }} {{ numSecond }} = </div>
            <div class="col-5 col-sm-3 col-md-2">
                <b-form-input id="input-valid" v-model="userResult" @keyup.enter="afterMoreClick" type="number"
                    autofocus autocomplete="off">
                </b-form-input>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <b-button variant="outline-info" v-on:click="afterMoreClick">Далее</b-button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            numFirst: 0,
            numSecond: 0,
            sign: "+",
            userResult: "",
        }
    },
    props: [ "themeProps" ],
    methods: {
        afterMoreClick() {
            let result;
            switch(this.sign) {
                case '-':
                    result = this.numFirst - this.numSecond;
                    break;
                case '/':
                    result = this.numFirst / this.numSecond;
                    break;
                case '*':
                    result = this.numFirst * this.numSecond;
                    break;
                default:
                    result = this.numFirst + this.numSecond;
                break;
            }
            this.$emit('checkTask', { 
                value: ((this.userResult != "" && +this.userResult === result) ? true : false),
                taskValue: this.numFirst + " " + this.sign + " " + this.numSecond + " = " + 
                    (this.userResult == "" ? "?" : this.userResult),
            });
            this.newTask();
        },
        newTask(lim = 10, sign = "+", numFirst, numSecond) {
            function randomNumber(min, max) {
                return Math.floor(Math.random() * (max - min + 1)) + min;
            }
            this.userResult = "";
            if (this.themeProps.limit) lim = this.themeProps.limit;
            this.numFirst = numFirst || randomNumber(0, lim);
            lim = lim - this.numFirst;
            if (lim > this.numFirst) lim = this.numFirst;
            this.numSecond = numSecond || randomNumber(0, lim);
            this.sign = sign;
            if (this.themeProps.signs) {
                let arrSigns = this.themeProps.signs.split(" ").filter(value => ["+", "-", "/", "*"].includes(value));
                this.sign = arrSigns[Math.floor(Math.random() * arrSigns.length)];
            }
            if (document.getElementById("input-valid")) document.getElementById("input-valid").focus();
        },
    },
    beforeMount() {
        this.newTask();
    },
}
</script>

<style scoped>
.row {
    padding: 0.5rem 0;
}
.task-view, #input-valid {
    font-size: 200%;
    font-weight: 700;
    height: 100%;
}
.task-view {
    text-align: right;
}
input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
  -webkit-appearance: none; 
  margin: 0; 
}
</style>