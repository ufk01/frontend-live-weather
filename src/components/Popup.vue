<template>
    <div class="popup" :class="positionClass">
        <slot></slot>
    </div>
</template>

<script>
export default {
    name: 'App',
    props: {
        position: {
            type: String,
            default: 'top-right'
        }
    },
    computed: {
        positionClass() {
            return `popup--${this.position}`;
        }
    }
}
</script>

<style scoped>
.popup {
    position: fixed;
    background: rgba(255, 255, 255, 0);
    color: #fff;
    padding: 10px 15px;
    border-radius: 6px;
    max-width: 250px;
    max-height: 250px;
    font-size: 0.9em;
    z-index: 1000;
    overflow-wrap: break-word;
    backdrop-filter: blur(8px);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
}
.popup--top-right {
    top: 10px;
    right: 10px;
}
.popup--top-left {
    top: 10px;
    left: 10px;
}
</style>
