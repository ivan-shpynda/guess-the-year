<script setup>
const MIN = 1900;
const MAX = 2026;

const props = defineProps({
    modelValue: Number,
});
const emit = defineEmits(["update:modelValue"]);

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
</script>

<template>
    <div :class="$style.wrapper">
        <div :class="$style.pillWrap">
            <div :class="$style.pill">
                <label :for="`year-input`" :class="$style.srOnly"
                    >Відповідь</label
                >
                <input
                    id="year-input"
                    type="number"
                    :value="modelValue"
                    :min="MIN"
                    :max="MAX"
                    :class="$style.numberInput"
                    @change="onNumber"
                />
            </div>
        </div>
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
</template>

<style module>
.wrapper {
    width: 100%;
    margin-bottom: 36px;
}

.pillWrap {
    display: flex;
    justify-content: center;
    margin-bottom: 24px;
}

.srOnly {
    font-size: 11px;
    font-weight: 400;
    letter-spacing: 1.5px;
    color: #aaaaaa;
    text-transform: uppercase;
    margin-bottom: 14px;
}

.pill {
    background: #f6f6f6;
    border-radius: 18px;
    padding: 10px 26px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.numberInput {
    width: 120px;
    text-align: center;
    font-family: "IBM Plex Mono", monospace;
    font-size: 32px;
    font-weight: 700;
    color: #111111;
    border: none;
    background: transparent;
    outline: none;
}

.numberInput::-webkit-outer-spin-button,
.numberInput::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.slider {
    width: 100%;
    accent-color: #1e6fe0;
    margin: 0 0 6px;
    cursor: pointer;
}

.labels {
    display: flex;
    justify-content: space-between;
}

.label {
    font-size: 11px;
    color: #bbbbbb;
}
</style>
