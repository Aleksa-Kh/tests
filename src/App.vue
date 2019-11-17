<template>
    <div id="app" class="container">

        <nav class="navbar navbar-light bg-light">
          <span class="navbar-text">
            {{testTitle}}
          </span>
        </nav>

        <main v-if="showQuestionTaskCount" class="row">

          <div class="col"><b-button variant="outline-info" v-on:click="showTasks(15)">15 заданий</b-button></div>
          <div class="col"><b-button variant="outline-info" v-on:click="showTasks(25)">25 заданий</b-button></div>
          <div class="col"><b-button variant="outline-info" v-on:click="showTasks(30)">30 заданий</b-button></div>
          <div class="col"><b-button variant="outline-info" v-on:click="showTasks(40)">40 заданий</b-button></div>
            
        </main>
  
        <main v-else class="row">

          <div v-if="itemNumber <= taskCount" class="col" >

            <div class="progress_figures">
              <p v-for="(item, index) in arrResultsR" v-bind:key="index" class="progress_circle" 
                  :class="{'bg-success': item.value == 1, 'bg-danger': item.value == 2}">
                  {{index + 1}}
              </p>
            </div>

            <div class="jumbotron">

              <h3>Вопрос № {{itemNumber}}</h3>

              <p class="lead">Тема: {{taskThemes[arrResultsR[itemNumber-1].theme].name}}</p>

              <component :is = "taskThemes[arrResultsR[itemNumber-1].theme].component"
                  :themeProps = "taskThemes[arrResultsR[itemNumber-1].theme].themeProps"
                  @checkTask = "checkTaskHandler"></component>

            </div>

          </div>

          <div v-else class="col">

            <div class="jumbotron">

              <h3>Результаты:</h3>

              <div class="container alert" 
                v-for="(value, name) in taskThemes" v-bind:key="name"
                :class="{'alert-success' : (value.rightTasks/value.totalTasks*100) > 82,
                'alert-warning' : ((value.rightTasks/value.totalTasks*100) > 50 && (value.rightTasks/value.totalTasks*100) <=82),
                'alert-danger' : (value.rightTasks/value.totalTasks*100) <= 50,}"
                >

                <p class="lead">Тема: {{value.name}}</p>

                <div class="row">

                  <div class="col">Всего заданий:</div>
                  <div class="col">{{value.totalTasks}}</div>

                </div>
                <div class="row">

                  <div class="col">Выполнено верно:</div>
                  <div class="col">{{value.rightTasks}} ({{(value.rightTasks/value.totalTasks*100)}}%)</div>

                </div>

              </div>

              <b-button variant="outline-success" v-on:click="afterRepeatClick">Повторить тестирование</b-button>

            </div>

          </div>

        </main>

    </div>
</template>

<script>
import SumSimple from './components/SumSimple.vue'

export default {
  name: 'TestTasks',
  components: {
    SumSimple,
  },
  data(){
    return {
      testTitle: "Математика",
      taskCount: 20, // количество заданий в тесте
      showQuestionTaskCount: true, // показать диалог выбора количества заданий
      taskThemes: { // темы заданий
        'Summ': {
          isEnabled: true, // использовать в тесте
          persentTasks: 50, // количество заданий от общего, %
          name: "Арифметические операции", // название темы
          component: "SumSimple", // используемый компонент
          themeProps: {
            signs: "- +", // знаки операций, разделенные пробелом
            limit: 15, // максимальная сумма цифр
          },
          totalTasks: 0, // всего заданий по этой теме (автоматически)
          rightTasks: 0, // всего верных заданий (автоматически)
        },
      },
      itemNumber: 1, // номер выведенного задания
    }
  },
  computed: {
    arrResultsR: function(){ // массив результатов
      // TODO: сгенерировать разные темы в зависимости от процентов в настройках
      let themes = Array.from({ length: this.taskCount }, v => "Summ");
      let arr = [];
      for (let index = 0; index < this.taskCount; index++) {
        arr.push({ theme: themes[index], value: 0 });
      }
      return arr;
    }
  },
  methods: {
    checkTaskHandler (item) {
      this.taskThemes[this.arrResultsR[this.itemNumber - 1].theme].totalTasks++;
      if (item.value == true) {
        this.arrResultsR[this.itemNumber - 1].value = 1;
        this.taskThemes[this.arrResultsR[this.itemNumber - 1].theme].rightTasks ++;
      }
      else {
        this.arrResultsR[this.itemNumber - 1].value = 2;
      }
      this.itemNumber += 1;
    },
    afterRepeatClick() {
      this.itemNumber = 1;
      for (let key in this.arrResultsR) {
        this.arrResultsR[key].value = 0;
      }
      for (let key in this.taskThemes) {
        this.taskThemes[key].totalTasks = 0;
        this.taskThemes[key].rightTasks = 0;
      }
      this.showQuestionTaskCount = true;
    },
    showTasks(count) {
      this.taskCount = count;
      this.showQuestionTaskCount = false;
    }
  }
  
}
</script>

<style lang="scss">
#app {
    background: url(./assets/bg1.png) 0 0 repeat;
    height: 100vh;
    min-width: 420px;
}
.progress_figures {
    line-height: 40px;
}
.progress_circle {
    width: 40px; 
    height: 40px;
    background: #e9ecef;
    border-radius: 50%;
    display: inline-block;
    text-align: center;
}
.jumbotron {
  text-align: center;
}
</style>
