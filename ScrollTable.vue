<!--
 * @Author: Pluto
 * @Date: 2025-03-18
 * @GitHub: https://github.com/Pidanyo/el-reconstruction-components
 * @Source: https://blog.csdn.net/moses_binson/article/details/144245530
-->
<template>
    <el-table ref="scrollTable" :data="tableData" height="400">
        <!-- 列配置 -->
    </el-table>
</template>
<script>
export default {
    components: {},
    data() {
        return {
            tableData: [],
            page: 1,
            pageSize: 10,
            total: 100, // 假设总数据量为 100
        }
    },
    computed: {},
    mounted() {
        /**
         * @description:添加分页加载滚动事件
         * @return {*}
         */
        let table = this.$refs.scrollTable.bodyWrapper
        table.addEventListener('scroll', (e) => {
            const { scrollTop, clientHeight, scrollHeight } = e.target
            // 检查是否滚动到底部
            if (scrollHeight - scrollTop <= clientHeight) {
                this.page++
                if ((this.page - 1) * this.pageSize >= this.total) {
                    // 数据已全部加载完毕
                    return
                }
                this.getTableData()
            }
        })
    },
 
    created() {
        this.getTableData()
    },
    methods: {
 
        /**
         * @description:获取表格数据
         * @return {*}
         */
        getTableData() {
            let params = {
                size: this.pageSize,
                current: this.page
            }
            this.$http({ method: 'get', url: `/hello/william/page?`, params })
                .then((res) => {
                    this.tableData.push(...res.data.records)
                    this.total = res.data.total
                })
                .finally(() => { })
        },
 
    }
}
</script>