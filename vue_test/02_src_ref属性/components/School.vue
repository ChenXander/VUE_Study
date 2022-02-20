<template>
    <div class="demo">
        <h2>学校名称：{{name}}</h2>
        <h2>学校地址：{{address}}</h2>
    </div>
</template>

<script>
export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'School',
    data() {
        return {
            name: 'VUE',
            address: '北京',
        }
    },
}
</script>

<style>
    .demo {
        background-color: pink;
    }
</style>