<template>
    <div class="container" :class="'class'+itemNumber">
        <div class="row row-center">
            <div class="col" :class="{'task-view': tasks[taskKey].word.length < 13}">
                {{ tasks[taskKey].word.substring(0, tasks[taskKey].word.indexOf('~')) }}
                <span v-if="tasks[taskKey].word.includes('~')" class="text-success">{{(userResult?userResult:"?")}}</span>
                {{ tasks[taskKey].word.substring(tasks[taskKey].word.indexOf('~')+1) }}
            </div>
        </div>
        <div class="row">
            <div class="col">
                <b-form-group v-if="isShowSelect">
                    <b-form-radio-group
                        id="btn-radio"
                        v-model="userResult"
                        :options="radioOptions"
                        button-variant="outline-success"
                        buttons
                        :stacked="isRadioStacked"
                    ></b-form-radio-group>
                </b-form-group>
                <b-form-input v-if="!isShowSelect" id="input-valid" v-model="userResult" @keyup.enter="afterMoreClick"
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
import zhishi from '../tasks/zhishi.js'
import chkchn from '../tasks/chkchn.js'
import myagkyy from '../tasks/myagkyy.js'
import slovslova from '../tasks/slovslova.js'
import bezudglasn from '../tasks/bezudglasn.js'
import matemdict from '../tasks/matemdict.js'

export default {
    data() {
        return {
            taskKey: 0,
            userResult: "",
        }
    },
    props: ["themeProps", "itemNumber"],
    computed: {
        taskNumber() {
            return this.itemNumber;
        },
        tasks() {
            switch (this.themeProps.theme) {
                case "zhishi":
                    return zhishi;
                case "chkchn":
                    return chkchn;
                case "myagkyy":
                    return myagkyy;
                case "slovslova":
                    return slovslova;
                case "bezudglasn":
                    return bezudglasn;
                case "matemdict":
                    return matemdict;
            
                default:
                    return zhishi;
            }
        },
        radioOptions() {
            return this.tasks[this.taskKey].all.concat(this.tasks[this.taskKey].correct).sort(() => Math.random() - 0.5);
        },
        isRadioStacked() {
            let optionMaxLenght = 0;
            for (let index = 0; index < this.radioOptions.length; index++) {
                if (this.radioOptions[index].length > optionMaxLenght) {
                    optionMaxLenght = this.radioOptions[index].length;
                }
            }
            if (optionMaxLenght > 3) {
                return true;
            }

            return false;
        },
        isShowSelect() {
            let isShow = true;
            if (this.radioOptions.length < 2) {
                isShow = false;
            }
            return isShow;
        }
    },
    methods: {
        generateTaskKey() {
            let min = 0;
            let max = this.tasks.length - 1;
            this.taskKey = Math.floor(Math.random() * (max - min + 1)) + min;
            this.userResult = "";
        },
        afterMoreClick() {
            this.$emit('checkTask', { 
                value: ((this.userResult === this.tasks[this.taskKey].correct) ? true : false),
                taskValue: '"' + this.tasks[this.taskKey].word.replace("~", "[?]") + '"  Ответ: "' +
                this.userResult + '"  Верный ответ: "' + this.tasks[this.taskKey].correct + '"',
            });
        },
    },
    watch: {
        taskNumber: function(){
            this.generateTaskKey();
        }
    },
    beforeMount() {
        this.generateTaskKey();
    },
}
</script>

<style scoped>
.row {
    padding: 0.5rem 0;
}
.task-view {
    font-size: 200%;
    font-weight: 700;
    height: 100%;
    line-height: 2;
    white-space: nowrap;
}
input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
  -webkit-appearance: none; 
  margin: 0; 
}
#input-valid {
    width: 80px;
    font-size: 200%;
    font-weight: 700;
    margin: 0 auto;
}
</style>