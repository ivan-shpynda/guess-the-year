<script setup>
defineProps({
    question: String,
});
</script>

<template>
    <div :class="$style.card">
        <p :class="$style.label">Подія</p>
        <p :class="$style.text">{{ question }}</p>
    </div>
</template>

<style module>
.card {
    width: 100%;
}

.label {
    font-size: 11px;
    font-weight: 400;
    letter-spacing: 1.5px;
    color: #aaaaaa;
    text-transform: uppercase;
    margin-bottom: 14px;
}

.text {
    font-size: 19px;
    font-weight: 400;
    color: #111111;
    line-height: 1.5;
}
</style>
