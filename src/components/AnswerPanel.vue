<script setup>
import { ref, watch, nextTick } from "vue";
import { animate } from "motion";

const MIN = 1900;
const MAX = 2026;

const props = defineProps({
    modelValue: Number,
    phase: { type: String, default: "playing" },
    correctYear: { type: Number, default: null },
    diff: { type: Number, default: null },
    lives: { type: Number, default: 0 },
});
const emit = defineEmits(["update:modelValue"]);

const answerPillEl = ref(null);
const correctPillEl = ref(null);
const deviationEl = ref(null);
const livesTextEl = ref(null);

const PILL_DURATION = 1.1;
const TEXT_DURATION = 0.4;
const PILL_EASING = [0.22, 1, 0.36, 1];
const FLIP_EASING = [0.16, 1, 0.3, 1];
const STAGGER_GAP = 0.4;

watch(
    () => props.phase,
    async (newPhase, oldPhase) => {
        if (newPhase !== "feedback" || oldPhase !== "playing") return;

        const prevRect = answerPillEl.value?.getBoundingClientRect();
        await nextTick();

        if (answerPillEl.value) {
            const newRect = answerPillEl.value.getBoundingClientRect();
            const deltaX = prevRect ? prevRect.left - newRect.left : 0;
            if (deltaX) {
                animate(
                    answerPillEl.value,
                    { x: [deltaX, 0] },
                    { duration: PILL_DURATION * 1.2, easing: FLIP_EASING },
                );
            }
        }

        if (correctPillEl.value) {
            animate(
                correctPillEl.value,
                { opacity: [0, 1], x: [16, 0] },
                { duration: PILL_DURATION, easing: PILL_EASING },
            );
        }

        if (deviationEl.value) {
            animate(
                deviationEl.value,
                { opacity: [0, 1], y: [8, 0] },
                {
                    duration: TEXT_DURATION,
                    easing: PILL_EASING,
                    delay: STAGGER_GAP,
                },
            );
        }

        if (livesTextEl.value) {
            animate(
                livesTextEl.value,
                { opacity: [0, 1], y: [8, 0] },
                {
                    duration: TEXT_DURATION,
                    easing: PILL_EASING,
                    delay: STAGGER_GAP * 2,
                },
            );
        }
    },
);

function onRange(e) {
    emit("update:modelValue", Number(e.target.value));
}

function onNumber(e) {
    let val = Number(e.target.value);
    if (!isNaN(val) && val >= MIN && val <= MAX) {
        emit("update:modelValue", val);
    } else if (!isNaN(val)) {
        emit("update:modelValue", Math.min(MAX, Math.max(MIN, val)));
    }
}

function pluralYears(n) {
    if (n % 100 >= 11 && n % 100 <= 19) return "років";
    const mod = n % 10;
    if (mod === 1) return "рік";
    if (mod >= 2 && mod <= 4) return "роки";
    return "років";
}

</script>

<template>
    <div :class="$style.wrapper">
        <div :class="$style.pillsRow">
            <div ref="answerPillEl" :class="$style.pillWrap">
                <div :class="$style.pill">
                    <label :for="`year-input`" :class="$style.pillLabel"
                        >Відповідь</label
                    >
                    <input
                        id="year-input"
                        type="number"
                        :value="modelValue"
                        :min="MIN"
                        :max="MAX"
                        :disabled="phase === 'feedback'"
                        :class="$style.numberInput"
                        @change="onNumber"
                    />
                </div>
            </div>

            <div v-if="phase === 'feedback'" ref="correctPillEl" :class="$style.pillWrap">
                <div :class="[$style.pill, $style.pillCorrect]">
                    <span :class="$style.pillLabel">Правильно</span>
                    <span :class="$style.correctYear">{{ correctYear }}</span>
                </div>
            </div>
        </div>

        <div v-if="phase === 'playing'" :class="$style.sliderArea">
            <input
                type="range"
                :min="MIN"
                :max="MAX"
                :value="modelValue"
                :class="$style.slider"
                @input="onRange"
            />
            <div :class="$style.labels">
                <span :class="$style.label">{{ MIN }}</span>
                <span :class="$style.label">{{ MAX }}</span>
            </div>
        </div>

        <div
            v-if="phase === 'feedback' && diff !== null"
            ref="deviationEl"
            :class="$style.deviation"
        >
            <template v-if="diff === 0">Точно!</template>
            <template v-else
                >Відхилення:
                <span :class="$style.deviationNumber">{{ diff }}</span>
                {{ pluralYears(diff) }}</template
            >
        </div>
        <div ref="livesTextEl" :class="$style.lives">Життя: {{ lives }}</div>
    </div>
</template>

<style module>
:root {
    --color-red-bg: rgba(255, 59, 48, 0.15);
}

.wrapper {
    width: 100%;
    margin-bottom: 36px;
    min-height: 180px;
}

.pillsRow {
    display: flex;
    gap: 12px;
    margin-bottom: 32px;
    justify-content: space-between;
}

.pillWrap {
    flex: 1;
    min-width: 0;
    display: flex;
    justify-content: center;
}

.pill {
    background: var(--color-bg-soft);
    border-radius: 18px;
    padding: 10px 22px;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
}

.pillCorrect {
    background: var(--color-green-bg);
}

.pillLabel {
    font-size: 11px;
    font-weight: 400;
    letter-spacing: 1.5px;
    color: var(--color-text-muted);
    text-transform: uppercase;
    margin-bottom: 4px;
}

.numberInput {
    width: 120px;
    text-align: center;
    font-family: "IBM Plex Mono", monospace;
    font-size: 32px;
    font-weight: 700;
    color: var(--color-text);
    border: none;
    background: transparent;
    outline: none;
}

.numberInput:disabled {
    color: var(--color-text);
    -webkit-text-fill-color: var(--color-text);
    opacity: 1;
}

.numberInput::-webkit-outer-spin-button,
.numberInput::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.correctYear {
    font-size: 32px;
    font-weight: 700;
    color: var(--color-green);
    line-height: 1.2;
}

.sliderArea {
    width: 100%;
}

.slider {
    width: 100%;
    accent-color: var(--color-blue);
    margin: 0 0 6px;
    cursor: pointer;
}

.labels {
    display: flex;
    justify-content: space-between;
}

.label {
    font-size: 11px;
    color: var(--color-text-faint);
}

.deviation {
    text-align: center;
    font-size: 16px;
    color: var(--color-text);
    letter-spacing: 0.5px;
    padding: 4px 0;
}

.deviationNumber {
    background: var(--color-red-bg);
    border-radius: 4px;
    padding: 1px 6px;
}

.lives {
    text-align: center;
    font-size: 14px;
    color: var(--color-text-muted);
    letter-spacing: 0.5px;
    padding: 4px 0;
}
</style>
