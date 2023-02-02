<template>
    <div class="component-list" @dragstart="handleDragStart">
        <div
            v-for="(item, index) in componentList"
            :key="index"
            class="list"
            draggable
            :data-index="index"
        >
            <span v-if="item.icon.substr(0,2) === 'el'" :class="item.icon"></span>
            <span v-else class="iconfont" :class="'icon-' + item.icon"></span>
            <div class="text">{{ item.label }}</div>
        </div>
    </div>
</template>

<script>
import componentList from '@/custom-component/component-list'

export default {
    data() {
        return {
            componentList,
        }
    },
    methods: {
        handleDragStart(e) {
            e.dataTransfer.setData('index', e.target.dataset.index)
        },
    },
}
</script>

<style lang="scss" scoped>
.component-list {
    height: 65%;
    padding: 10px;
    display: grid;
    grid-gap: 10px 19px;
    grid-template-columns: repeat(auto-fill, 80px);
    grid-template-rows: repeat(auto-fill, 40px);

    .list {
        width: 80px;
        height: 48px;
        border: 2px solid #ddd;
        margin-bottom: 1px !important;
        cursor: grab;
        text-align: center;
        color: #441e1e;
        padding: 2px 5px;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;

        &:active {
            cursor: grabbing;
        }

        .iconfont {
            margin-right: 4px;
            font-size: 20px;
        }

        .icon-wenben,
        .icon-biaoge {
            font-size: 18px;
        }

        .icon-tupian {
            font-size: 16px;
        }

        .text {
            font-size: 5px;
            font-family: sans-serif;
            margin-top: 3px;
        }
    }
}
</style>
