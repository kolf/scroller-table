<template>
  <div id="app">
    <ScrollerTable
      title="营业区期交（季度&年度)"
      filter-field="BRANCH_NAME"
      @filter="onFilter"
      :data-level="3"
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
          title: "市场<br/>排名",
          field: "BRANCH_NAME",
          fixed: true,
        },
        {
          title: "银行<br/>渠道",
          field: "BRANCH_CODE",
          fixed: true,
        },
        {
          title: "个人期交",
          field: "AREA_NAME",
          render(text, record, index) {
            if (index === 0) {
              return `<div height='82'><div class='table-col'>国寿</div><div class='table-col'>太平</div><div class='table-col'>渤海</div></div>`;
            }
            return text;
          },
        },
        {
          title: "该渠道前三公司",
          children: [
            {
              title: "公司名称",
              field: "AREA_CODE",
            },
            {
              title: "月度期交",
              field: "DEPT_NAME",
            },
            {
              title: "渠道份额",
              filed: "DEPT_CODE",
            },
          ],
        },
        {
          title: "个人期交",
          field: "DEPT_NAME",
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

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.table-col {
  width: 71px;
  line-height: 27px;
  border-bottom: 1px solid #eee;
  &:last-child {
    border-bottom: 0;
  }
}
</style>
