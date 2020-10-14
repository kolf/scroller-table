<template>
  <div id="app">
    <ScrollerTable
      title="营业区期交（季度&年度)"
      filter-field="BRANCH_NAME"
      @filter="onFilter"
      :data-level='2'
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
          render(text, record) {
            if (text === "一支") {
              return `<i style='color:#f00'>${text}</i>`;
            }
            console.log(text, record, "record");
            return text;
          },
        },
        {
          title: "营业区",
          field: "AGENT_STATUS",
        },
        {
          title: "月度承保",
          field: "Y11111",
          children: [
            {
              title: "月度</br>期交",
              field: "D_UNDERWRITE_PREMIUM",
            },
            {
              title: "月度目标",
              field: "M_UNDERWRITE_PREMIUM",
            },
          ],
        },
        {
          title: "月度环比",
          field: "Y11112",
          children: [
            {
              title: "月度</br>期交",
              field: "Q_UNDERWRITE_PREMIUM",
            },
            {
              title: "月度目标",
              field: "Y_UNDERWRITE_PREMIUM",
            },
          ],
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
      footerData: this.makeFooterData(), //首次计算好的合计
    };
  },
  methods: {
    onFilter(filterField, filterValue) {
      this.footerData = this.makeFooterData(filterField, filterValue); //筛选全重新计算合计
    },
    makeFooterData(filterField, filterValue) {
      console.log(filterField, filterValue, "通过选中的字段和值计算新的合计");
      return [
        "3%",
        "111",
        {
          //可传对象
          text: "90个",
          width: 72 * 3 + "px",
        },
      ];
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
