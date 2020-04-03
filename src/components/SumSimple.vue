<template>
    <div class="container">
        <div class="row">
            <div class="col">
                <div :class="{'task-view': taskString.length < 13}">
                    {{ taskString.substring(0, taskString.indexOf('~')) }}
                    <span v-if="taskString.includes('~')" class="text-success">{{(userResult?userResult:"?")}}</span>
                    {{ taskString.substring(taskString.indexOf('~')+1) }}
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <b-form-input class="task-view" id="input-valid" v-model="userResult" @keyup.enter="afterMoreClick" type="number"
                    autofocus autocomplete="off"></b-form-input>
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
import templates from '../tasks/_templates.js'

export default {
    data() {
        return {
            numFirst: 0,
            numSecond: 0,
            sign: "+",
            userResult: "",
        }
    },
    props: [ "themeProps", "itemNumber" ],
    computed: {
        taskNumber() {
            return this.itemNumber;
        },
        taskString() {
            let mString = this.template.replace("[1]", this.numFirst).replace("[s]", this.sign).replace("[2]", this.numSecond);
            return mString;
        },
        template() {
            let temp = "[1] [s] [2] = ~";

            if (this.themeProps.template && this.taskNumber % 2) {
                let arTemplates = [];
                switch (this.themeProps.template) {
                    case "templates":
                        arTemplates = templates;

                    default:
                        arTemplates = templates;
                }
                let arNewTemplates = [temp];
                let sign = this.sign;
                arTemplates.forEach(function(item, index, array) {
                    if (item.signs == sign) {
                        arNewTemplates = arNewTemplates.concat(item.strings);
                    }
                });
                let min = 0;
                let max = arNewTemplates.length - 1;
                temp = arNewTemplates[Math.floor(Math.random() * (max - min + 1)) + min];
            }

            return temp;
        },
    },
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
                taskValue: '"' + this.taskString.replace("~", "[?]") + '"  Ответ: "' +
                this.userResult + '"  Верный ответ: "' + result + '"',
            });
        },
        newTask(lim = 10, sign = "+", numFirst, numSecond) {
            function randomNumber(min, max) {
                return Math.floor(Math.random() * (max - min + 1)) + min;
            }
            this.sign = sign;
            if (this.themeProps.signs) {
                let arrSigns = this.themeProps.signs.split(" ").filter(value => ["+", "-", "/", "*"].includes(value));
                this.sign = arrSigns[Math.floor(Math.random() * arrSigns.length)];
            }
            this.userResult = "";
            if (this.themeProps.limit) lim = this.themeProps.limit;
            // при умножении первый множитель [1...10]
            if (["*", "/"].includes(this.sign) && lim > 10) lim = 10;
            this.numFirst = numFirst || randomNumber(0, lim);
            // при умножении второй множитель задается лимитом
            if (["*", "/"].includes(this.sign)) lim = 10;
            if (["+", "-"].includes(this.sign)) {
                lim = lim - this.numFirst;
                if (lim > this.numFirst) lim = this.numFirst;
            }
            this.numSecond = numSecond || randomNumber(0, lim);
            if (this.sign == "/") {
                let multi = this.numFirst * this.numSecond;
                if (this.numSecond != 0) {
                    this.numFirst = multi;
                } else {
                    this.numSecond = this.numFirst;
                    this.numFirst = multi;
                    if (this.numFirst == this.numSecond == 0) {
                        this.sign = "*"
                    }
                }
            }

            if (document.getElementById("input-valid")) document.getElementById("input-valid").focus();
        },
    },
    beforeMount() {
        this.newTask();
    },
    watch: {
        taskNumber: function(){
            this.newTask();
        }
    },
}
</script>

<style scoped>
.row {
    padding: 0.5rem 0;
}
.task-view{
    font-size: 200%;
    font-weight: 700;
    margin: 0 auto;

}
.taskString {
    font-size: 50%;
    font-weight: 300;
}
#input-valid {
    width: 90px;
    font-size: 200%;
    font-weight: 700;
    margin: 0 auto;
}
input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
