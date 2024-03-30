<template>
  <div class="page-content">
    <div class="table">
      <el-table
        :data="data"
        border
        style="width: 100%"
        v-bind="contentConfig.childrenProps"
        @selection-change="handleSelectionChange"
        ref="tableRef"
      >
        <template v-for="item in contentConfig.propsList">
          <template v-if="item.type === 'tag'">
            <el-table-column v-bind="item">
              <template #default="scope">
                <el-tag v-bind="item.tags[scope.row[item.prop]]">
                  {{ item.tags[scope.row[item.prop]].text }}
                </el-tag>
              </template>
            </el-table-column>
          </template>
          <template v-else-if="item.type === 'custom'">
            <el-table-column v-bind="item">
              <template #default="scope">
                <slot :name="item.slotName" v-bind="scope"></slot>
              </template>
            </el-table-column>
          </template>
          <template v-else>
            <el-table-column v-bind="item" />
          </template>
        </template>
      </el-table>
    </div>
    <div
      class="pagination"
      v-if="showPagination"
      :style="{ justifyContent: getPagePostion }"
    >
      <el-pagination
        v-model:current-page="currentPage"
        v-model:page-size="pSize"
        ref="paginationRef"
        :page-sizes="[10, 20, 40, 80]"
        layout="total, sizes, prev, pager, next, jumper"
        :total="pageTotalCount"
        background
        v-bind="paginationConfig"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>
  </div>
</template>
<script>
  export default {
    components: {},
    props: {
      contentConfig: {
        type: Object,
        default: () => ({
          propsList: [],
        }),
      },
      data: {
        type: Array,
        default: () => [],
      },
      showPagination: {
        type: Boolean,
        default: true,
      },
      pageTotalCount: {
        type: Number,
        default: 0,
      },
      paginationConfig: {
        type: Object,
        default: () => ({}),
      },
      paginationPostion: {
        type: String,
        default: "right", // 'left' | 'center' | 'right'
      },
      pageSize: {
        type: Number,
        default: 10,
      },
    },
    data() {
      return {
        currentPage: 1,
        pSize: 10,
      };
    },
    watch: {
      pageSize() {
        this.pSize = this.pageSize;
      },
    },
    computed: {
      getPagePostion() {
        switch (this.paginationPostion) {
          case "right":
            return "flex-end";
          case "center":
            return "center";
          default:
            return "flex-start";
        }
      },
    },
    methods: {
      paginationChange() {
        const queryInfo = { pageNum: currentPage.value, pageSize: pSize.value };
        this.$emit("paginationChange", queryInfo);
      },
      handleSizeChange(val) {
        this.pSize = val;
        paginationChange();
      },
      handleCurrentChange() {
        paginationChange();
      },
      handleSelectionChange(rows) {
        this.$emit("selectionChange", rows);
      },
    },
  };
</script>
<style lang="scss" scoped>
  .pagination {
    margin-top: 10px;
    display: flex;
  }
</style>
