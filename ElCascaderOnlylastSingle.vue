<!--
 * @Author: Pluto
 * @Date: 2025-03-18
 * @GitHub: https://github.com/Pidanyo/el-reconstruction-components
-->
<template>
    <div>
        <el-cascader v-model="selectedPaths"  style="width: 100%;" :options="options" ref="cascader" filterable collapse-tags :props="cascaderProps"
            @change="handleChange" popper-class="custom-cascader">
            <template slot-scope="{ node, data }">
                <span class="node-label" v-if="node.isLeaf && data.isLastNode">{{ data.label }}</span>
                <span class="unleaf" v-else>{{ data.label }}</span>
            </template>
        </el-cascader>
    </div>
</template>

<script>
export default {
    name:"ElCascaderOnlylastSingle",
    props: {
        options: {
            type: Array,
            default: () => []
        },
        value:{
            type: Array,
            default: () => []
        },
        cascaderProps:{
            type: Object,
            default:{
                value:"id",
                multiple: true
            }
        },
    },
    data() {
        return {
            selectedPaths: [],
            lastSelectedPaths: [],
        };
    },
    watch:{
        value:{
            handler(val) {
                this.selectedPaths = val
            },
            deep:true,
            immediate:true
        },
    },
    methods: {
        handleChange(currentValue) {

            // 1. 找出新增的路径（通过路径字符串比对）
            const newPaths = currentValue.filter(path =>
                !this.lastSelectedPaths.some(oldPath =>
                    this.pathEqual(oldPath, path)
                )
            )

            // 2. 处理每个新增路径
            let finalPaths = [...currentValue]
            newPaths.forEach(newPath => {

                // 获取父路径（移除最后一级）
                const parentPath = newPath.slice(0, -1)

                // 3. 过滤同父路径的其他兄弟
                finalPaths = finalPaths.filter(path => {
                    // 当前路径的父路径（可能不存在）
                    const currentParent = path.slice(0, -1)

                    return (
                        // 保两种情况：
                        // 1. 不属于当前父级的路径
                        !this.pathEqual(currentParent, parentPath) ||
                        // // 2. 就是新增路径本身
                         this.pathEqual(path, newPath) 
                    )
                })
            })

            finalPaths = finalPaths.filter(path=>{
                return !this.pathEqual(path, newPaths[0])
            })

            if(newPaths[0]){
                finalPaths.unshift(newPaths[0])
            }

            this.selectedPaths = [...finalPaths]
            // 4. 保存当前状态
            this.lastSelectedPaths = [...finalPaths]

            this.$emit("update", this.selectedPaths)
            this.$emit("change", this.selectedPaths)
        },
        // 辅助方法：路径数组比对
        pathEqual(a, b) {
            return JSON.stringify(a) === JSON.stringify(b)
        },
    }
};
</script>

<style>
.custom-cascader .el-cascader-node:has(.unleaf) .el-checkbox {
    display: none;
}

.custom-cascader .el-cascader-node:has(.node-label) .el-checkbox__inner {
    border-radius: 50%;
}
</style>