<template>
    <div>
        <b-jumbotron>
            <template slot="lead">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item 
                    v-for="(answer,index) in shuffledAnswers" 
                    :key="index"
                    @click="selectAnswer(index)"
                    :class="answerClass(index)"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button variant="primary"
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered"
            >Submit</b-button>
            <b-button @click="next" variant="success" href="#">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    import _ from 'lodash'

    export default {
        props: {
            // props are variables imported from the parent component. In order to accept them, they must be declared here
            currentQuestion: Object,
            next: Function,
            increment: Function
        },
        data() {
            return {
                selectedIndex: null,
                correctIndex: null,
                shuffledAnswers: [],
                answered: false
            }
        },
        computed: {
            answers() {
                //  The ellipses is known as a "spread". When used on an array (the namespaced variable in this case) that's INSIDE 
                // another array, the original array's values split and inserted into the new array 
                let answers = [...this.currentQuestion.incorrect_answers]
                answers.push(this.currentQuestion.correct_answer)
                return answers
            }
        },
        watch: {
            // Watch is looking for changes on variables, listed as object keys. When a variable is changed, the handler is called
            currentQuestion: {
                // immediate: true means the watch handler will run on the initial pass
                immediate: true,
                handler() {
                    this.selectedIndex = null
                    this.answered = false
                    this.shuffleAnswers()
                }
            }
        },
        methods: {
            selectAnswer(index) {
                this.selectedIndex = index
            },
            shuffleAnswers() {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
                this.shuffledAnswers = _.shuffle(answers)
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            },
            submitAnswer() {
                let isCorrect = false

                if(this.selectedIndex === this.correctIndex){
                    isCorrect = true
                }
                this.answered = true

                this.increment(isCorrect)
            },
            answerClass(index) {
                let answerClass = ''

                if(!this.answered && this.selectedIndex == index){
                    answerClass = 'selected'
                } else if(this.answered && this.correctIndex === index) {
                    answerClass = 'correct'
                } else if(this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                    answerClass = 'incorrect'
                }

                return answerClass
            }
        }
    }
</script>

<style scoped>

    .list-group {
        margin-bottom: 15px;
    }

    .list-group-item:hover {
        background-color: #eee;
        cursor: pointer; 
    }

    .btn {
        margin: 0 5px;
    }

    .selected {
        background-color: lightblue;
    }

    .correct {
        background-color: lightgreen;
    }

    .incorrect {
        background-color: red;
    }

</style>
