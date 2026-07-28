<script setup>
import { ref, computed } from "vue";
import questionsData from "./data/questions.json";
import GameHeader from "./components/GameHeader.vue";
import QuestionCard from "./components/QuestionCard.vue";
import AnswerPanel from "./components/AnswerPanel.vue";
import GameOver from "./components/GameOver.vue";
import PageHeader from "./components/PageHeader.vue";
import IntroScreen from "./components/IntroScreen.vue";

const STARTING_LIVES = 100;

function shuffle(arr) {
    const a = [...arr];
    for (let i = a.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [a[i], a[j]] = [a[j], a[i]];
    }
    return a;
}

function pluralYears(n) {
    return n === 1 ? "year" : "years";
}

const lives = ref(STARTING_LIVES);
const selectedYear = ref(1963);
const currentIndex = ref(0);
const questionNum = ref(1);
const correctAnswers = ref(0);
const shuffledQuestions = ref(shuffle(questionsData));
const phase = ref("intro");
const lastResult = ref(null);

const currentQuestion = computed(
    () => shuffledQuestions.value[currentIndex.value],
);

const isLastQuestion = computed(
    () => lives.value <= 0 || currentIndex.value + 1 >= shuffledQuestions.value.length,
);

function startGame() {
    phase.value = "playing";
}

function confirm() {
    const correct = currentQuestion.value.year;
    const guess = selectedYear.value;
    const diff = Math.abs(guess - correct);
    const livesLost = diff;
    lives.value = Math.max(0, lives.value - livesLost);
    if (diff === 0) {
        correctAnswers.value++;
    }
    lastResult.value = {
        diff,
        correctYear: correct,
        guessYear: guess,
        livesLost,
    };
    phase.value = "feedback";
}

function nextQuestion() {
    if (isLastQuestion.value) {
        phase.value = "gameover";
        return;
    }
    currentIndex.value++;
    questionNum.value++;
    phase.value = "playing";
    lastResult.value = null;
}

function restart() {
    lives.value = STARTING_LIVES;
    selectedYear.value = 1963;
    currentIndex.value = 0;
    questionNum.value = 1;
    correctAnswers.value = 0;
    shuffledQuestions.value = shuffle(questionsData);
    phase.value = "playing";
    lastResult.value = null;
}
</script>

<template>
    <div :class="$style.app">
        <PageHeader />
        <div :class="$style.card">
            <template v-if="phase === 'intro'">
                <IntroScreen @start="startGame" />
            </template>

            <template v-else-if="phase === 'gameover'">
                <GameOver
                    :questions-answered="questionNum"
                    :correct-answers="correctAnswers"
                    @restart="restart"
                />
            </template>

            <template v-else>
                <GameHeader :lives="lives" :question-num="questionNum" />
                <QuestionCard :question="currentQuestion.text" />
                <AnswerPanel
                    v-model="selectedYear"
                    :lives="lives"
                    :phase="phase"
                    :correct-year="lastResult?.correctYear ?? null"
                    :diff="lastResult?.diff ?? null"
                />

                <template v-if="phase === 'playing'">
                    <button :class="$style.mainBtn" @click="confirm">
                        CONFIRM
                    </button>
                </template>

                <template v-else>
                    <button :class="$style.mainBtn" @click="nextQuestion">
                        {{ isLastQuestion ? "RESULTS" : "NEXT QUESTION" }}
                    </button>
                </template>
            </template>
        </div>
    </div>
</template>

<style>
:root {
    --color-text: #111111;
    --color-text-muted: #aaaaaa;
    --color-text-faint: #bbbbbb;
    --color-text-medium: #555555;
    --color-text-gray: #999999;
    --color-text-brown: #9a8f80;

    --color-blue: #1e6fe0;
    --color-blue-light: #dce9ff;

    --color-green: #2fbd6b;
    --color-green-bg: #e8f5ee;

    --color-yellow: #ffd200;
    --color-yellow-shadow: #d4a900;
    --color-yellow-text: #7a5a00;

    --color-white: #ffffff;
    --color-bg: #f4f4f4;
    --color-bg-soft: #f6f6f6;
    --color-shadow: #ececec;
    --color-divider: #eeeeee;
    --color-divider-soft: #f0f0f0;
    --color-overlay: rgba(0, 0, 0, 0.5);

    color-scheme: light;
}

*,
*::before,
*::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    background: var(--color-bg);
    font-family: "IBM Plex Mono", "Courier New", monospace;
    min-height: 100vh;
    color: var(--color-text);
}
</style>

<style module>
.app {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 24px 16px 64px;
    gap: 16px;
}

.card {
    background: var(--color-white);
    border-radius: 28px;
    padding: 44px 34px;
    box-shadow: 0 10px 0 var(--color-shadow);
    width: 100%;
    max-width: 380px;
}

.livesBlock {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: var(--color-blue);
    border-radius: 16px;
    padding: 16px 22px;
    margin-bottom: 20px;
}

.livesBlockLabel {
    font-size: 12px;
    letter-spacing: 1px;
    color: var(--color-blue-light);
    text-transform: uppercase;
}

.livesBlockValue {
    font-size: 22px;
    font-weight: 700;
    color: var(--color-white);
}

.mainBtn {
    width: 100%;
    background: var(--color-yellow);
    color: var(--color-yellow-text);
    border: none;
    border-radius: 16px;
    padding: 16px;
    font-family: inherit;
    font-size: 13px;
    font-weight: 700;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    cursor: pointer;
    box-shadow: 0 5px 0 var(--color-yellow-shadow);
    transition: filter 0.1s;
}

.mainBtn:hover {
    filter: brightness(1.04);
}

.mainBtn:active {
    filter: brightness(0.97);
}
</style>
