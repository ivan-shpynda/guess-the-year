<script setup>
import { ref, watch } from "vue";
import { animate } from "motion";

const props = defineProps({
    lives: Number,
    questionNum: Number,
});

const livesEl = ref(null);
const tileBlueEl = ref(null);
const tileYellowEl = ref(null);
const displayedLives = ref(props.lives);

watch(
    () => props.lives,
    (newVal, oldVal) => {
        const livesLost = Math.abs(oldVal - newVal);
        animate(oldVal, newVal, {
            duration: (livesLost * 50) / 1000,
            easing: [0.22, 1, 0.36, 1],
            onUpdate: (value) => {
                displayedLives.value = Math.round(value);
            },
        });

        if (newVal < oldVal && tileBlueEl.value) {
            animate(
                tileBlueEl.value,
                { scale: [1, 1.2, 1] },
                { duration: 0.3, easing: [0.22, 1, 0.36, 1] },
            );
        }
    },
);

watch(
    () => props.questionNum,
    (newVal, oldVal) => {
        if (newVal > oldVal && tileYellowEl.value) {
            animate(
                tileYellowEl.value,
                { scale: [1, 1.2, 1] },
                { duration: 0.3, easing: [0.22, 1, 0.36, 1] },
            );
        }
    },
);
</script>

<template>
    <div :class="$style.header">
        <h1 :class="$style.title">Вгадай рік</h1>
        <div :class="$style.tiles">
            <div ref="tileBlueEl" :class="$style.tileBlue">
                <div :class="$style.tileLabel">ЖИТТЯ</div>
                <div ref="livesEl" :class="$style.tileValue">
                    {{ displayedLives }}
                </div>
            </div>
            <div ref="tileYellowEl" :class="$style.tileYellow">
                <div :class="[$style.tileLabel, $style.tileLabelDark]">
                    ПИТАННЯ
                </div>
                <div :class="[$style.tileValue, $style.tileValueDark]">
                    {{ questionNum }}
                </div>
            </div>
        </div>
    </div>
</template>

<style module>
.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 36px;
}

.title {
    font-size: 14px;
    font-weight: 700;
    letter-spacing: 0.5px;
    color: var(--color-text);
    text-transform: uppercase;
}

.tiles {
    display: flex;
    gap: 8px;
}

.tileBlue {
    background: var(--color-blue);
    border-radius: 12px;
    padding: 6px 12px;
    text-align: center;
}

.tileYellow {
    background: var(--color-yellow);
    border-radius: 12px;
    padding: 6px 12px;
    text-align: center;
}

.tileLabel {
    font-size: 9px;
    letter-spacing: 1px;
    color: var(--color-blue-light);
}

.tileLabelDark {
    color: var(--color-yellow-text);
}

.tileValue {
    font-size: 15px;
    font-weight: 700;
    color: var(--color-white);
}

.tileValueDark {
    color: var(--color-yellow-text);
}
</style>
