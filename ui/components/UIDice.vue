<template>
    <div className="ui-dice-wrapper">
        <div v-if="value % 6 === 1" class="face face-1">
            <span class="dot" />
        </div>
        <div v-else-if="value % 6 === 2" class="face face-2">
            <span class="dot" />
            <span class="dot" />
        </div>
        <div v-else-if="value % 6 === 3" class="face face-3">
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
        </div>
        <div v-else-if="value % 6 === 4" class="face face-4">
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
        </div>
        <div v-else-if="value % 6 === 5" class="face face-5">
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
        </div>
        <div v-else class="face face-6">
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
            <span class="dot" />
        </div>
    </div>
</template>

<script setup>
import { computed, inject, onMounted, watch } from 'vue'
import { useStore } from 'vuex'

const props = defineProps({
    id: { type: String, required: true },
    props: { type: Object, default: () => ({}) },
    state: { type: Object, default: () => ({ enabled: false, visible: false }) }
})

const $dataTracker = inject('$dataTracker')
const store = useStore()

const messages = computed(() => store.state.data.messages)
// Sometimes `props.id` is not yet registered in `store.messages` when onMounted is triggered.
// This watcher monitors the `messages` state until `props.id`  is present.
const unwatch = watch(messages, () => {
    $dataTracker(props.id)
    if (messages.value[props.id]) {
        unwatch()
    }
}, { deep: true, immediate: true })

const value = computed(() => {
    switch (props.props.valueType) {
    case 'msg': {
        const msg = messages.value[props.id]
        if (msg && Object.prototype.hasOwnProperty.call(msg, props.props.value)) {
            return msg[props.props.value]
        }
        break
    }
    case 'str':
        return parseInt(props.props.value)
    case 'num':
        return props.props.value
    }
    return 1
})

onMounted(() => {
    // setup our event handlers, and informs Node-RED that this widget has loaded
    $dataTracker(props.id)
})
</script>

<style scoped>
.ui-dice-wrapper {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 2px;
}
.face {
    padding: 8px;
    background-color: #fff;
    width: 108px;
    height: 108px;
    border-radius: 4px;
    border: 1px solid #222;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    gap: 4px;
}
.dot {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-color: #333;
    align-self: center;
    justify-self: center;
}
.face-1 .dot {
    grid-area: 2 / 2;
}
.face-2 .dot:nth-child(1) {
    grid-area: 1 / 1;
}
.face-2 .dot:nth-child(2) {
    grid-area: 3 / 3;
}
.face-3 .dot:nth-child(1) {
    grid-area: 1 / 1;
}
.face-3 .dot:nth-child(2) {
    grid-area: 2 / 2;
}
.face-3 .dot:nth-child(3) {
    grid-area: 3 / 3;
}
.face-4 .dot:nth-child(1) {
    grid-area: 1 / 1;
}
.face-4 .dot:nth-child(2) {
    grid-area: 1 / 3;
}
.face-4 .dot:nth-child(3) {
    grid-area: 3 / 1;
}
.face-4 .dot:nth-child(4) {
    grid-area: 3 / 3;
}
.face-5 .dot:nth-child(1) {
    grid-area: 1 / 1;
}
.face-5 .dot:nth-child(2) {
    grid-area: 1 / 3;
}
.face-5 .dot:nth-child(3) {
    grid-area: 2 / 2;
}
.face-5 .dot:nth-child(4) {
    grid-area: 3 / 1;
}
.face-5 .dot:nth-child(5) {
    grid-area: 3 / 3;
}
.face-6 .dot:nth-child(1) {
    grid-area: 1 / 1;
}
.face-6 .dot:nth-child(2) {
    grid-area: 2 / 1;
}
.face-6 .dot:nth-child(3) {
    grid-area: 1 / 3;
}
.face-6 .dot:nth-child(4) {
    grid-area: 3 / 1;
}
.face-6 .dot:nth-child(5) {
    grid-area: 2 / 3;
}
.face-6 .dot:nth-child(6) {
    grid-area: 3 / 3;
}
</style>
