<template>
  <div id="app">
    <ScrollerTable
      title="营业区期交（季度&年度)"
      filter-field="BRANCH_NAME"
      v-on:change="onFilter"
      :body-height="320"
      :columns="columns"
      :data-source="dataSource"
      :footer-data="footerData"
    />
  </div>
</template>

<script>
import ScrollerTable from "./components/ScrollerTable.vue";
import data from "./data/orderList.json";

export default {
  name: "App",
  components: {
    ScrollerTable,
  },
  data() {
    return {
      columns: [
        {
          title: "期交</br>排行",
          field: "INDEX",
          fixed: true,
        },
        {
          title: "支公司",
          field: "BRANCH_NAME",
          fixed: true,
        },
        {
          title: "营业区",
          field: "AGENT_STATUS",
        },
        {
          title: "月度</br>期交",
          field: "D_UNDERWRITE_PREMIUM",
        },
        {
          title: "月度目标",
          field: "M_UNDERWRITE_PREMIUM",
        },
        {
          title: "月度</br>达成",
          field: "Q_UNDERWRITE_PREMIUM",
        },
        {
          title: "年度期交",
          field: "Y_UNDERWRITE_PREMIUM",
        },
      ],
      dataSource: data.body.result.map((item, index) => ({
        ...item,
        INDEX: index + 1,
      })),
      footerData: ["32%", 1, 2, 3, 4, 5],//首次计算好的合计
    };
  },
  methods: {
    onFilter(selectedValue) {
      console.log(selectedValue, "选中的筛选项");
      this.footerData = [1, "10%", 2, 3, 4, 5]; //筛选全重新计算合计
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
