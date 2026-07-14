<script setup>
defineEmits(["start"]);
</script>

<template>
    <div :class="$style.screen">
        <div :class="$style.top">
            <p :class="$style.label">Вгадай рік</p>
            <p :class="$style.text">
                Перед тобою — історична подія. Твоє завдання — вгадати рік,
                коли вона відбулася, за допомогою слайдера.
            </p>
        </div>

        <ul :class="$style.rules">
            <li :class="$style.rule">
                <span :class="$style.ruleIcon">●</span>
                Чим більше відхилення від правильного року, тим більше життів
                втрачаєш
            </li>
            <li :class="$style.rule">
                <span :class="$style.ruleIcon">●</span>
                Гра триває, доки не закінчаться життя
            </li>
            <li :class="$style.rule">
                <span :class="$style.ruleIcon">●</span>
                Наприкінці побачиш, скільки питань пройшов
            </li>
        </ul>

        <div :class="$style.divider" />

        <button :class="$style.startBtn" @click="$emit('start')">
            ПОЧАТИ
        </button>
    </div>
</template>

<style module>
.screen {
    display: flex;
    flex-direction: column;
    gap: 24px;
}

.top {
    display: flex;
    flex-direction: column;
    gap: 12px;
}

.label {
    font-size: 11px;
    font-weight: 400;
    letter-spacing: 1.5px;
    color: var(--color-text-muted);
    text-transform: uppercase;
}

.text {
    font-size: 17px;
    font-weight: 400;
    color: var(--color-text);
    line-height: 1.5;
}

.rules {
    display: flex;
    flex-direction: column;
    gap: 10px;
    list-style: none;
}

.rule {
    display: flex;
    align-items: flex-start;
    gap: 10px;
    font-size: 13px;
    color: var(--color-text);
    line-height: 1.5;
}

.ruleIcon {
    color: var(--color-blue);
    font-size: 8px;
    margin-top: 5px;
    flex-shrink: 0;
}

.divider {
    height: 1px;
    background: var(--color-divider);
}

.startBtn {
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

.startBtn:hover {
    filter: brightness(1.04);
}

.startBtn:active {
    filter: brightness(0.97);
}
</style>
