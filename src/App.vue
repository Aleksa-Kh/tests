<template>
    <div id="app" class="container-fluid">
        <b-navbar toggleable="sm" type="light" variant="light">
          <!-- <b-navbar-brand href="#">Тестирование</b-navbar-brand> -->

          <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

          <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav>
              <b-nav-item v-for="(value, index) in lessons" v-bind:key="index" :to="{name: 'tasklesson', params: { lesson: index }}">
                {{value.title}}
              </b-nav-item>
              
            </b-navbar-nav>
          </b-collapse>
          
        </b-navbar>

        <main v-if="showQuestionTaskCount" class="row">

          <h3 class="col-12">Выберите количество заданий:</h3>
          <div v-for="count in arrTaskCount" class="col-lg-3 col-sm-4 col-xs-12 p-2">
            <b-button class="show_task" variant="outline-info" v-on:click="showTasks(count)">{{count}}</b-button>
          </div>

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

              <!-- <p class="lead">Тема: {{taskThemes[arrResultsR[itemNumber-1].theme].name}}</p> -->

              <component :is = "taskThemes[arrResultsR[itemNumber-1].theme].component"
                  :themeProps = "taskThemes[arrResultsR[itemNumber-1].theme].themeProps"
                  :itemNumber = "itemNumber"
                  @checkTask = "checkTaskHandler"></component>

            </div>

          </div>

          <div v-else class="col">

            <div class="jumbotron">

              <h3>Результаты:</h3>

              <div class="container alert" 
                v-for="(value, name) in taskThemes" v-if="value.totalTasks>0" v-bind:key="name"
                :class="{'alert-success' : (value.rightTasks/value.totalTasks*100) > 82,
                'alert-warning' : ((value.rightTasks/value.totalTasks*100) > 50 && (value.rightTasks/value.totalTasks*100) <=82),
                'alert-danger' : (value.rightTasks/value.totalTasks*100) <= 50,}"
                >

                <p class="lead">Тема: "{{value.name}}"</p>

                <div class="row">

                  <div class="col">Всего заданий:</div>
                  <div class="col">{{value.totalTasks}}</div>

                </div>
                <div class="row">

                  <div class="col">Выполнено верно:</div>
                  <div class="col">{{value.rightTasks}} ({{Math.round(value.rightTasks/value.totalTasks*100)}}%)</div>

                </div>
                <div class="row border-top border-dark" v-if="value.rightTasks<value.totalTasks">
                  <div class="col">
                    <strong>Ошибки:</strong>
                    <div  v-for="xval in value.errorTasksValue">
                      {{xval}}
                    </div>
                  </div>
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
import SelectLetter from './components/SelectLetter.vue'

export default {
  name: 'TestTasks',
  components: {
    SumSimple,
    SelectLetter,
  },
  data(){
    return {
      taskCount: 20, // количество заданий в тесте
      showQuestionTaskCount: true, // показать диалог выбора количества заданий
      arrTaskCount: [5, 10, 20, 30], // количество заданий для диалога выбора
      lessons: {
        maths: {
          title: "Математика",
          themes: ['Summ'],
        },
        russian: {
          title: "Русский язык",
          themes: ['ZhiShi', 'ChkChn', 'Myagkyy'],
        },
      },
      taskThemes: { // темы заданий
        'Summ': {
          isEnabled: true, // использовать в тесте
          persentTasks: 50, // количество заданий от общего, %
          name: "Арифметические операции", // название темы
          component: "SumSimple", // используемый компонент
          themeProps: {
            signs: "- +", // знаки операций, разделенные пробелом
            limit: 20, // максимальная сумма цифр
          },
          totalTasks: 0, // всего заданий по этой теме (автоматически)
          rightTasks: 0, // всего верных заданий (автоматически)
          errorTasksValue: [], // неверные задания (автоматически)
        },
        'ZhiShi': {
          isEnabled: true, // использовать в тесте
          persentTasks: 50, // количество заданий от общего, %
          name: "ЖИ-ШИ, ЧА-ЩА, ЧУ-ЩУ", // название темы
          component: "SelectLetter", // используемый компонент
          themeProps: {
            theme: "zhishi",
          },
          totalTasks: 0, // всего заданий по этой теме (автоматически)
          rightTasks: 0, // всего верных заданий (автоматически)
          errorTasksValue: [], // неверные задания (автоматически)
        },
        'ChkChn': {
          isEnabled: true, // использовать в тесте
          persentTasks: 50, // количество заданий от общего, %
          name: "ЧК-ЧН", // название темы
          component: "SelectLetter", // используемый компонент
          themeProps: {
            theme: "chkchn",
          },
          totalTasks: 0, // всего заданий по этой теме (автоматически)
          rightTasks: 0, // всего верных заданий (автоматически)
          errorTasksValue: [], // неверные задания (автоматически)
        },
        'Myagkyy': {
          isEnabled: true, // использовать в тесте
          persentTasks: 50, // количество заданий от общего, %
          name: "Ь — показатель мягкости", // название темы
          component: "SelectLetter", // используемый компонент
          themeProps: {
            theme: "myagkyy",
          },
          totalTasks: 0, // всего заданий по этой теме (автоматически)
          rightTasks: 0, // всего верных заданий (автоматически)
          errorTasksValue: [], // неверные задания (автоматически)
        },
      },
      itemNumber: 1, // номер выведенного задания
    }
  },
  computed: {
    arrResultsR: function(){ // массив результатов
      // TODO: сгенерировать разные темы в зависимости от процентов в настройках
      let lessonKey = (this.$route.params.lesson ? this.$route.params.lesson : 'maths')
      let min = 0;
      let max = this.lessons[lessonKey].themes.length - 1;
      let themes = Array.from({ length: this.taskCount }, v => this.lessons[lessonKey].themes[Math.floor(Math.random() * (max - min + 1)) + min]);
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
        if (item.taskValue) {
          this.taskThemes[this.arrResultsR[this.itemNumber - 1].theme].errorTasksValue.push(item.taskValue);
        }
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
        this.taskThemes[key].errorTasksValue = [];
      }
      this.showQuestionTaskCount = true;
    },
    showTasks(count) {
      this.taskCount = count;
      this.showQuestionTaskCount = false;
    }
  },
}
</script>

<style lang="scss">
#app {
    background: url(./assets/bg1.png) 0 0 repeat;
    min-height: 100vh;
    min-width: 350px;
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
.show_task {
  width: 100%;
}
.router-link-active {
  font-weight: 700;
}
</style>
