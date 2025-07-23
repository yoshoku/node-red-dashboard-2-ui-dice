<template>
    <div className="dice-wrapper">
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

<script>
import { mapState } from 'vuex'

export default {
    name: 'Dice',
    inject: ['$socket', '$dataTracker'],
    props: {
        /* do not remove entries from this - Dashboard's Layout Manager's will pass this data to your component */
        id: { type: String, required: true },
        props: { type: Object, default: () => ({}) },
        state: { type: Object, default: () => ({ enabled: false, visible: false }) }
    },
    computed: {
        ...mapState('data', ['messages']),
        value: function () {
            return this.messages[this.id]?.payload || Math.floor(Math.random() * 6 + 1)
        }
    },
    created () {
        // setup our event handlers, and informs Node-RED that this widget has loaded
        this.$dataTracker(this.id, this.onInput, this.onLoad, this.onDynamicProperties)
    },
    methods: {
        /*
            widget-action just sends a msg to Node-RED, it does not store the msg state server-side
            alternatively, you can use widget-change, which will also store the msg in the Node's datastore
        */
        send (msg) {
            this.$socket.emit('widget-action', this.id, msg)
        },
        /*
            (optional) Custom onInput function to handle incoming messages from Node-RED
        */
        onInput (msg) {
            // load the latest message from the Node-RED datastore when this widget is loaded
            // storing it in our vuex store so that we have it saved as we navigate around
            this.$store.commit('data/bind', {
                widgetId: this.id,
                msg
            })
        },
        /*
            (optional) Custom onLoad function to handle the loading state of the widget
            msg   - the latest message from the Node-RED datastore
            state - The Node-RED config, including any overrides saved to the server-side statestore
        */
        onLoad (msg, state) {
            // loads the last msg received into this node from the Node-RED datastore
            // state is auto-stored into the widget props, but is available here if you want to do anything else
        },
        /*
            (optional) Custom onDynamicProperties function to handle dynamic properties
            msg - the latest message from the Node-RED datastore
        */
        onDynamicProperties (msg) {
            // handle any dynamic properties that are sent from Node-RED
            const updates = msg.ui_update
            if (!updates) {
                return
            }
            if (typeof updates.example !== 'undefined') {
                // use the globally available "setDynamicProperties" function to store any updates to this property
                this.setDynamicProperties({ example: updates.example })
            }
        }
    }
}
</script>

<style scoped>
.dice-wrapper {
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
