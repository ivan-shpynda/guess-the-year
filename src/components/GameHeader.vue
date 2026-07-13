<script setup>
defineProps({
    lives: Number,
    questionNum: Number,
});
</script>

<template>
    <div :class="$style.header">
        <span :class="$style.title">Вгадай рік</span>
        <div :class="$style.tiles">
            <div :class="$style.tileBlue">
                <div :class="$style.tileLabel">ЖИТТЯ</div>
                <div :class="$style.tileValue">{{ lives }}</div>
            </div>
            <div :class="$style.tileYellow">
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
}

.title {
    font-size: 14px;
    font-weight: 700;
    letter-spacing: 0.5px;
    color: #111111;
}

.tiles {
    display: flex;
    gap: 8px;
}

.tileBlue {
    background: #1e6fe0;
    border-radius: 12px;
    padding: 6px 12px;
    text-align: center;
}

.tileYellow {
    background: #ffd200;
    border-radius: 12px;
    padding: 6px 12px;
    text-align: center;
}

.tileLabel {
    font-size: 9px;
    letter-spacing: 1px;
    color: #dce9ff;
}

.tileLabelDark {
    color: #7a5a00;
}

.tileValue {
    font-size: 15px;
    font-weight: 700;
    color: #ffffff;
}

.tileValueDark {
    color: #7a5a00;
}
</style>
