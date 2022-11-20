<template>
    <div class="modal" v-show="show">
        <slot></slot>
    </div>

</template>
<script>
export default {
    props: {
        show: Boolean,
    }
}
</script>
<style>
.modal{
    position: absolute;
    background-color: #f2f2f2;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    padding: 20px 30px;
    border: 1px solid rgba(0, 0, 0, 0.3);
    box-shadow: 0 3px 7px rgba(0, 0, 0, 0.3);
}
</style>