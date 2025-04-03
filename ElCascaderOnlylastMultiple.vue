<!--
 * @Author: Pluto
 * @Date: 2025-03-18
 * @GitHub: https://github.com/Pidanyo/el-reconstruction-components
-->
<template>
    <div>
        <el-cascader v-model="selectedPaths" style="width: 100%;" :options="options" ref="cascader" filterable
            collapse-tags :props="cascaderProps" @change="handleChange" popper-class="custom-cascader">
            <template slot-scope="{ node, data }">
                <span class="node-label" v-if="node.isLeaf && data.isUserNode">{{ data.label }}</span>
                <span class="unleaf" v-else>{{ data.label }}</span>
            </template>
        </el-cascader>
    </div>
</template>

<script>
export default {
    name: "ElCascaderOnlylastMultiple",
    props: {
        options: {
            type: Array,
            default: () => []
        },
        value: {
            type: Array,
            default: () => []
        },
        cascaderProps: {
            type: Object,
            default: () => ({
                value: "id",
                multiple: true
            })
        },
    },
    data() {
        return {
            selectedPaths: [],
            lastSelectedPaths: [],
        };
    },
    watch: {
        value: {
            handler(val) {
                this.selectedPaths = val
            },
            deep: true,
            immediate: true
        },
    },
    methods: {
        handleChange(currentValue) {
            this.$emit("update", this.selectedPaths)
            this.$emit("change", this.selectedPaths)
        },

    }
};
</script>

<style>
.custom-cascader .el-cascader-node:has(.unleaf) .el-checkbox {
    display: none;
}

</style>