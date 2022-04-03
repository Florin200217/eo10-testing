<template>
  <div v-if="isCatchyOn" class="catchy">
    <h1>TIME TO BE JUDGED!</h1>
  </div>
  <div class="main-wrapper">
    <div v-if="isFirstLoading" class="first-loading-screen">
      <img src="../assets/loading.svg" alt="Loading screen" />
    </div>
    <div v-if="!isFirstLoading && !isCatchyOn" class="second-wrapper">
      <div class="question">
        <h1>{{question_body.question_id}}. {{ question_body.question_text }}</h1>
        <div v-for="option in question_body.answer_choices" :key="option">
          <h2>{{ option }}</h2>
        </div>
        <div class="button-1">
          <button v-on:click="nextQuestion()">Next Question</button>
        </div>
      </div>
      <div class="yellow-line"></div>
      <div v-if="isSecondLoading" class="second-loading-screen">
        <img src="../assets/loading.svg" alt="Loading screen" />
      </div>
      <div class="answers" v-if="show && !isSecondLoading">
        <h1>Participants' Answers</h1>
        <div class="teams-wrapper">
          <div class="teams" v-for="answer in answers.answers" :key="answer">
            <h2 class="team">{{ answer.team }}</h2>
            <h3 class="answer">{{ answer.answer }}</h3>
          </div>
        </div>
        <div class="button-2">
          <button v-on:click="showAnswers()">Show answers</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      question_body: {},
      answers: {
        answers: [],
      },
      show: true,
      isCatchyOn: true,
      isFirstLoading: false,
      isSecondLoading: false,
      clickShowAndwersNumberOfTimes: 2,
    };
  },
  mounted() {
    setTimeout(() => {
      this.isCatchyOn = false;
    }, 4000);
  },
  methods: {
    nextQuestion: function () {
      this.isFirstLoading = true;
      axios
        .get("http://10.41.210.123:12666/get_next_question", {
          timeout: 120000,
        })
        .then((data) => {
          this.question_body = data.data;
          console.log(this.question_body);
          this.isFirstLoading = false;
        })
        .catch((e) => console.log(e.code, e.message, e.stack));
      this.answers.answers = [];
      this.clickShowAndwersNumberOfTimes = 0;
    },
    showAnswers: function () {
      this.clickShowAndwersNumberOfTimes += 1;
      if (this.clickShowAndwersNumberOfTimes <= 1) {
        this.isSecondLoading = true;
        axios
          .get("http://10.41.210.123:12666/send_next_question", {
            timeout: 120000,
          })
          .then((data) => {
            this.answers = data.data;
            console.log(this.answers);
            this.isSecondLoading = false;
          })
          .catch((e) => console.log(e.code, e.message, e.stack));
        this.show = true;
      } else {
        alert(
          "Cannot click Show Answers more than once before clicking Next Question"
        );
      }
    },
  },
};
</script>

<style>
.catchy {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2rem;
}

.first-loading-screen {
  display: flex;
  justify-content: center;
  align-content: center;
  height: 100vh;
}

.first-loading-screen img {
  max-width: 30vw;
}

.question {
  color: #e5e5e5;
  padding-top: 50px;
  padding-bottom: 50px;
  background-color: #1b3256;
}

.question h1 {
  display: inline-block;
  margin-bottom: 30px;
  padding: 5px 10px;
  border: 2px solid #eeab3f;
  border-radius: 10px;
}

.question h2 {
  margin: 15px 0;
}

.button-1 {
  margin-top: 50px;
}

.yellow-line {
  height: 5px;
  width: 100%;
  background-color: #eeab3f;
}

.answers {
  padding-top: 50px;
  min-height: 63vh;
  background-color: #e5e5e5;
  /* display: grid; */
}

.answers h1 {
  display: inline-block;
  padding: 5px 10px;
  border: 2px solid #1b3256;
  border-radius: 10px;
  margin-bottom: 30px;
}

.answers h2,
h3 {
  margin: 15px 0;
  display: inline-block;
}

.team {
  font-size: 1rem;
}

.answer {
  font-size: 0.8rem;
}

.teams-wrapper{
  display: grid;
  grid-template-columns: 50% 50%;
  /* grid-column-gap: ; */
}

.teams {
  display: grid;
  grid-template-columns: 1fr 1fr;
  width: 90%;
  margin: 0 auto;
  text-align: left;
  padding: 0 20px;
  border-bottom: 1px solid #eeab3f;
  /* border-width: 70%; */
}

.team {
  word-break: break-word;
}

.answer {
  /* word-break: break-word */
  overflow: hidden;
}

.button-2 {
  background-color: #e5e5e5;
  padding-top: 30px;
  padding-bottom: 50px;
}

button {
  width: 200px;
  height: 40px;
  border-radius: 10px;
  background-color: #eeab3f;
  font-size: 1rem;
  font-weight: 600;
  border: none;
}
</style>