<template>
    <div class="question-box-container">
        <b-jumbotron>

            <template v-slot:lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">
            <b-list-group>
                <b-list-group-item
                    v-for="(answer, index) in answers"
                    :key="index"
                    v-on:click="selectAnswer(index)"
                    :class="answerClass(index)">
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>
            <b-button variant="primary" href="#" v-on:click="submitAnswer" :disabled="selectedIndex===null || answered">Submit</b-button>
            <b-button variant="success" href="#" v-on:click="nextQuestion">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import lodash from 'lodash'
export default {
    props: {
        currentQuestion: Object,
        nextQuestion: Function,
        increment: Function
    },
    data() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers:[],
            answered : false
        }
    },
    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
            return answers;
        } 
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = null;
                this.answered = false;
                this.shffleAnswers();
            }
        }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index;
        },
        submitAnswer() {
            let isCorrect = false;
            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true;
            }
            this.answered = true
            this.increment(isCorrect);
        },
        shffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
            this.shuffledAnswers = lodash.shuffle(answers);
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
        },
        answerClass(index) {
            let answerClass = '';
            if(!this.answered && this.selectedIndex === index) {
                answerClass = 'selected';
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct';
            } else if (this.answered && this.correctIndex !== index && this.selectedIndex === index) {
                answerClass = 'incorrect';
            }

            return answerClass;
        }
    }
}
</script>

<style scoped>
.list-group {
    margin-bottom: 15px;
}

.list-group-item:hover {
    background: #EEE;
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
    background: red;
}

</style>