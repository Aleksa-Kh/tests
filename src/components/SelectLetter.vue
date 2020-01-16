<template>
    <div class="container" :class="'class'+itemNumber">
        <div class="row row-center">
            <div class="col task-view">
                {{ tasks[taskKey].word.split('*')[0] }}
                <span class="text-success">{{userResult}}</span>
                {{ tasks[taskKey].word.split('*')[1] }}
            </div>
        </div>
        <div class="row">
            <div class="col">
                <b-form-group label="">
                    <b-form-radio-group
                        id="btn-radios-3"
                        v-model="userResult"
                        :options="radioOptions"
                        button-variant="outline-success"
                        buttons
                    ></b-form-radio-group>
                </b-form-group>
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

export default {
    data() {
        return {
            taskKey: 0,
            userResult: "?",
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
            
                default:
                    return zhishi;
            }
        },
        radioOptions() {
            return this.tasks[this.taskKey].all.concat(this.tasks[this.taskKey].correct).sort(() => Math.random() - 0.5);
        },
    },
    methods: {
        generateTaskKey() {
            let min = 0;
            let max = this.tasks.length - 1;
            this.taskKey = Math.floor(Math.random() * (max - min + 1)) + min;
            this.userResult = "?";
        },
        afterMoreClick() {
            this.$emit('checkTask', { 
                value: ((this.userResult === this.tasks[this.taskKey].correct) ? true : false),
                taskValue: this.tasks[this.taskKey].word.split('*')[0] +
                    this.userResult +
                    this.tasks[this.taskKey].word.split('*')[1],
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
</style>