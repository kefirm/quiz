<template>
    <div class="question-box-container">
        <b-jumbotron>

            <template v-slot:lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group
            v-for="(answer, index) in shuffledAnswers"
            :key="index"
            @click.prevent="selectAnswer(index)">
                <b-list-group-item
                        :class="answerClass(index)">
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>


            <b-button
                variant="primary"
                v-on:click="submitAnswer"
                :disabled="selectedIndex === null || answered">
                Submit
            </b-button>
            <b-button @click="next" variant="success" href="#">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    import _ from 'lodash'
    export default  {
        props: {
            currentQuestion: Object,
            next: Function,
            increment: Function
        },
        data() {
            return {
                selectedIndex: null,
                shuffledAnswers: null,
                correctIndex: null,
                answered: false
            }
        },
        computed: {
            answers() {
                let answers = [...this.currentQuestion.incorrect_answers]
                answers.push(this.currentQuestion.correct_answer)
                return answers
            }
        },
        watch: {
          currentQuestion: {
              immediate: true,
              handler() {
                  this.selectedIndex = null
                  this.answered = false
                  this.shuffleAnswers()
              }
          }
        },
        methods: {
            answerClass(index){
                let answerClass = ''
                if(!this.answered && this.selectedIndex === index ){
                    answerClass = 'selected'
                }
                else if ( this.answered && this.correctIndex === index) {
                    answerClass = 'correct'
                }
                else if (this.answered && this.selectedIndex ==index && this.correctIndex !== index ) {
                    answerClass = 'incorrect'
                }
                return answerClass

            },
            submitAnswer(){
              let isCorrect = false

              if (this.selectedIndex === this.correctIndex)
                  isCorrect = true
            this.answered = true
            this.increment(isCorrect)
            },
            selectAnswer(index) {
                this.selectedIndex = index
            },
            shuffleAnswers(){
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
                this.shuffledAnswers = _.shuffle(answers)
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            }
        },
        /*mounted() {
            this.shuffleAnswers()
        }*/
    }

</script>

<style scoped>
    .list-group{
        margin-bottom: 15px;
    }
    .list-group-item:hover {
        background-color: #EEE;
        cursor: pointer;
    }
    .btn {
        margin: 0 5px;
    }
    .selected {
        background-color: lightblue;
    }
    .correct {
        background-color: green;
    }
    .incorrect {
        background-color: red;
    }
</style>