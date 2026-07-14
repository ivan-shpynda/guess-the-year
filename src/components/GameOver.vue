<script setup>
defineProps({
    questionsAnswered: Number,
    correctAnswers: Number,
});
defineEmits(["restart"]);
</script>

<template>
    <div :class="$style.screen">
        <div :class="$style.top">
            <p :class="$style.label">ГРА ЗАВЕРШЕНА</p>
            <p :class="$style.bigScore">{{ questionsAnswered }}</p>
            <p :class="$style.sublabel">питань пройдено</p>
        </div>

        <div :class="$style.top">
            <p :class="$style.bigScore">{{ correctAnswers }}</p>
            <p :class="$style.sublabel">вгадано правильно</p>
        </div>

        <div :class="$style.divider" />

        <button :class="$style.restartBtn" @click="$emit('restart')">
            ГРАТИ ЗНОВУ
        </button>
    </div>
</template>

<style module>
.screen {
    display: flex;
    flex-direction: column;
    gap: 32px;
}

.top {
    display: flex;
    flex-direction: column;
    gap: 8px;
}

.label {
    font-size: 11px;
    font-weight: 400;
    letter-spacing: 1.5px;
    color: var(--color-text-muted);
    text-transform: uppercase;
    margin-bottom: 4px;
}

.bigScore {
    font-size: 72px;
    font-weight: 700;
    color: var(--color-text);
    line-height: 1;
    letter-spacing: -0.02em;
}

.sublabel {
    font-size: 13px;
    color: var(--color-text-muted);
    letter-spacing: 0.05em;
}

.divider {
    height: 1px;
    background: var(--color-divider);
}

.restartBtn {
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

.restartBtn:hover {
    filter: brightness(1.04);
}

.restartBtn:active {
    filter: brightness(0.97);
}
</style>
