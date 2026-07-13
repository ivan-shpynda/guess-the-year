<script setup>
import { ref, computed } from "vue";
import questionsData from "./data/questions.json";
import GameHeader from "./components/GameHeader.vue";
import QuestionCard from "./components/QuestionCard.vue";
import YearSlider from "./components/YearSlider.vue";
import GameOver from "./components/GameOver.vue";
import PageHeader from "./components/PageHeader.vue";

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
    if (n % 100 >= 11 && n % 100 <= 19) return "років";
    const mod = n % 10;
    if (mod === 1) return "рік";
    if (mod >= 2 && mod <= 4) return "роки";
    return "років";
}

const lives = ref(STARTING_LIVES);
const selectedYear = ref(1963);
const currentIndex = ref(0);
const questionNum = ref(1);
const shuffledQuestions = ref(shuffle(questionsData));
const phase = ref("playing");
const lastResult = ref(null);

const currentQuestion = computed(
    () => shuffledQuestions.value[currentIndex.value],
);

function confirm() {
    const correct = currentQuestion.value.year;
    const guess = selectedYear.value;
    const diff = Math.abs(guess - correct);
    const livesLost = diff;
    lives.value = Math.max(0, lives.value - livesLost);
    lastResult.value = { diff, correctYear: correct, guessYear: guess, livesLost };
    phase.value = "feedback";
}

function nextQuestion() {
    if (lives.value <= 0) {
        phase.value = "gameover";
        return;
    }
    const nextIndex = currentIndex.value + 1;
    if (nextIndex >= shuffledQuestions.value.length) {
        shuffledQuestions.value = shuffle(questionsData);
        currentIndex.value = 0;
    } else {
        currentIndex.value = nextIndex;
    }
    questionNum.value++;
    selectedYear.value = 1963;
    phase.value = "playing";
    lastResult.value = null;
}

function restart() {
    lives.value = STARTING_LIVES;
    selectedYear.value = 1963;
    currentIndex.value = 0;
    questionNum.value = 1;
    shuffledQuestions.value = shuffle(questionsData);
    phase.value = "playing";
    lastResult.value = null;
}
</script>

<template>
    <div :class="$style.app">
        <PageHeader />
        <div :class="$style.card">
            <template v-if="phase === 'gameover'">
                <GameOver
                    :questions-answered="questionNum"
                    @restart="restart"
                />
            </template>

            <template v-else>
                <div
                    :style="{
                        marginBottom: phase === 'playing' ? '48px' : '40px',
                    }"
                >
                    <GameHeader
                        :lives="lives"
                        :question-num="questionNum"
                    />
                </div>

                <div
                    :style="{
                        marginBottom: phase === 'playing' ? '52px' : '28px',
                    }"
                >
                    <QuestionCard :question="currentQuestion.text" />
                </div>

                <template v-if="phase === 'playing'">
                    <YearSlider v-model="selectedYear" />
                    <button :class="$style.mainBtn" @click="confirm">
                        ПІДТВЕРДИТИ
                    </button>
                </template>

                <template v-else>
                    <div :class="$style.detailsCard">
                        <div
                            :class="[$style.detailRow, $style.detailRowBorder]"
                        >
                            <span :class="$style.detailLabel">Відповідь</span>
                            <span :class="$style.detailValue">{{
                                lastResult.guessYear
                            }}</span>
                        </div>
                        <div
                            :class="[$style.detailRow, $style.detailRowBorder]"
                        >
                            <span :class="$style.detailLabel">Правильно</span>
                            <span
                                :class="[
                                    $style.detailValue,
                                    $style.detailValueCorrect,
                                ]"
                                >{{ lastResult.correctYear }}</span
                            >
                        </div>
                        <div :class="$style.detailRow">
                            <span :class="$style.detailLabel">Відхилення</span>
                            <span :class="$style.detailValueSmall">{{
                                lastResult.diff === 0
                                    ? "0 років"
                                    : lastResult.diff +
                                      " " +
                                      pluralYears(lastResult.diff)
                            }}</span>
                        </div>
                    </div>

                    <div :class="$style.livesBlock">
                        <span :class="$style.livesBlockLabel"
                            >Залишок життів</span
                        >
                        <span :class="$style.livesBlockValue">{{ lives }}</span>
                    </div>

                    <button :class="$style.mainBtn" @click="nextQuestion">
                        {{ lives <= 0 ? "РЕЗУЛЬТАТИ" : "НАСТУПНЕ ПИТАННЯ" }}
                    </button>
                </template>
            </template>
        </div>
    </div>
</template>

<style>
*,
*::before,
*::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    background: #f4f4f4;
    font-family: "IBM Plex Mono", "Courier New", monospace;
    min-height: 100vh;
    color: #111111;
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
    background: #ffffff;
    border-radius: 28px;
    padding: 44px 34px;
    box-shadow: 0 10px 0 #ececec;
    width: 100%;
    max-width: 380px;
}

.detailsCard {
    background: #f6f6f6;
    border-radius: 20px;
    padding: 28px 24px;
    margin-bottom: 16px;
}

.detailRow {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.detailRowBorder {
    padding-bottom: 18px;
    margin-bottom: 18px;
    border-bottom: 1px solid #e8e8e8;
}

.detailLabel {
    font-size: 11px;
    letter-spacing: 1.5px;
    color: #aaaaaa;
    text-transform: uppercase;
}

.detailValue {
    font-size: 24px;
    font-weight: 700;
    color: #111111;
}

.detailValueCorrect {
    color: #2fbd6b;
}

.detailValueSmall {
    font-size: 16px;
    font-weight: 700;
    color: #111111;
}

.livesBlock {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: #1e6fe0;
    border-radius: 16px;
    padding: 16px 22px;
    margin-bottom: 20px;
}

.livesBlockLabel {
    font-size: 12px;
    letter-spacing: 1px;
    color: #dce9ff;
    text-transform: uppercase;
}

.livesBlockValue {
    font-size: 22px;
    font-weight: 700;
    color: #ffffff;
}

.mainBtn {
    width: 100%;
    background: #ffd200;
    color: #7a5a00;
    border: none;
    border-radius: 16px;
    padding: 16px;
    font-family: inherit;
    font-size: 13px;
    font-weight: 700;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    cursor: pointer;
    box-shadow: 0 5px 0 #d4a900;
    transition: filter 0.1s;
}

.mainBtn:hover {
    filter: brightness(1.04);
}

.mainBtn:active {
    filter: brightness(0.97);
}
</style>
