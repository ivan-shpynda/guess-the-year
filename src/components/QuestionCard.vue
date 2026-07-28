<script setup>
defineProps({
    question: String,
});
</script>

<template>
    <div :class="$style.card">
        <p :class="$style.label">Event</p>
        <p :class="$style.text">{{ question }}</p>
    </div>
</template>

<style module>
.card {
    width: 100%;
    margin-bottom: 36px;
    min-height: 142px;
}

.label {
    font-size: 11px;
    font-weight: 400;
    letter-spacing: 1.5px;
    color: var(--color-text-muted);
    text-transform: uppercase;
    margin-bottom: 14px;
}

.text {
    font-size: 19px;
    font-weight: 400;
    color: var(--color-text);
    line-height: 1.5;
}
</style>
