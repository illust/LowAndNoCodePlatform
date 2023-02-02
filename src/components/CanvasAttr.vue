<template>
    <div class="attr-container">
        <p class="title">画布属性</p>
        <el-form style="padding: 20px;">
            <el-form-item v-for="(key, index) in Object.keys(options)" :key="index" :label="options[key]">
                <el-color-picker v-if="isIncludesColor(key)" v-model="canvasStyleData[key]" show-alpha></el-color-picker>
                <label v-else-if="key=='background'" for="input" class="insert">
                    插入图片
                    <input
                        id="input"
                        type="file"
                        hidden
                        @change="handleFileChange"
                    />
                </label>
                <el-input v-else v-model.number="canvasStyleData[key]" type="number" />
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
import generateID from '@/utils/generateID'
import { mapState } from 'vuex'
import toast from '@/utils/toast'
import { commonStyle, commonAttr } from '@/custom-component/component-list'
import { $ } from '@/utils/utils'
import { changeComponentSizeWithScale } from '@/utils/changeComponentsSizeWithScale'

export default {
    data() {
        return {
            options: {
                color: '颜色',
                opacity: '不透明度',
                backgroundColor: '背景色',
                fontSize: '字体大小',
                background: '背景图片',
            },
        }
    },
    computed: mapState([
        'canvasStyleData',
    ]),
    methods: {
        isIncludesColor(str) {
            return str.toLowerCase().includes('color')
        },
        handleFileChange(e) {
            const file = e.target.files[0]
            if (!file.type.includes('image')) {
                toast('只能插入图片')
                return
            }

            const reader = new FileReader()
            reader.onload = (res) => {
                const fileResult = res.target.result
                const img = new Image()
                img.onload = () => {
                    const component = {
                        ...commonAttr,
                        id: generateID(),
                        component: 'Picture',
                        label: '图片',
                        icon: '',
                        propValue: {
                            url: fileResult,
                            flip: {
                                horizontal: false,
                                vertical: false,
                            },
                        },
                        style: {
                            ...commonStyle,
                            top: 0,
                            left: 0,
                            width: img.width,
                            height: img.height,
                        },
                    }

                    console.log('hhhhh')
                    this.$store.commit('setCanvasBackground', fileResult)

                    // 根据画面比例修改组件样式比例 https://github.com/woai3c/visual-drag-demo/issues/91
                    changeComponentSizeWithScale(component)

                    this.$store.commit('addComponent', { component })
                    this.$store.commit('recordSnapshot')

                    // 修复重复上传同一文件，@change 不触发的问题
                    $('#input').setAttribute('type', 'text')
                    $('#input').setAttribute('type', 'file')
                }

                img.src = fileResult
            }

            reader.readAsDataURL(file)
        },
    },
}
</script>

<style lang="scss">
.attr-container {
    .title {
        text-align: center;
        margin-bottom: 10px;
        height: 40px;
        line-height: 40px;
        border-bottom: 2px solid #e4e7ed;
        font-size: 14px;
        font-weight: 500;
        color: #303133;
    }
}
</style>
