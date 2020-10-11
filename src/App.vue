<template>
  <div id="app">
    <ScrollerTable
      title="营业区期交（季度&年度)"
      :columns="columns"
      :dataSource="dataSource"
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
          title: "期交排行",
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
          title: "月度期交",
          field: "D_UNDERWRITE_PREMIUM",
        },
        {
          title: "月度目标",
          field: "M_UNDERWRITE_PREMIUM",
        },
        {
          title: "月度达成",
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
    };
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
