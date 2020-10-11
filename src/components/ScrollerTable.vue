<template>
  <div class="table__root">
    <div class="table__container">
      <div class="table__header">
        <div class="table__title">{{ title }}</div>
        <div class="table__header-row">
          <div class="table__fixed-left">
            <p
              v-for="item in getColumnList(true)"
              :key="item.field"
              class="col"
            >
              {{ item.title }}
            </p>
          </div>
          <div class="table__fixed-right">
            <div
              :style="{ overflowX: 'auto' }"
              @scroll="handleScroll($event)"
              ref="headerRight"
            >
              <div
                :style="{
                  width: getColumnList().length * 72 + 'px',
                }"
              >
                <p
                  v-for="item in getColumnList()"
                  :key="item.field"
                  class="col"
                >
                  {{ item.title }}
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="table__body" :style="{ height: bodyHeight + 'px' }">
        <div :style="{ height: fixedData.length * 36 + 'px', display: 'flex' }">
          <div class="table__fixed-left">
            <div v-for="(item, i) in fixedData" :key="i" class="row">
              <p v-for="(f, j) in item" :key="j" class="col">
                {{ item[j] || "---" }}
              </p>
            </div>
          </div>
          <div class="table__fixed-right">
            <div
              :style="{ overflowX: 'auto' }"
              ref="bodyRight"
              @scroll="handleScroll($event)"
            >
              <div
                :style="{
                  width: getColumnList().length * 72 + 'px',
                }"
              >
                <div v-for="(item, i) in autoData" :key="i" class="row">
                  <p v-for="(f, j) in item" :key="j" class="col">
                    {{ item[j] || "---" }}
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="table__footer">
        <div class="table__fixed-left">
          <div class="row">
            <p class="col" style="width: 100%">合计</p>
          </div>
        </div>
        <div class="table__fixed-right">
          <div
            :style="{ overflowX: 'auto' }"
            @scroll="handleScroll($event)"
            ref="footerRight"
          >
            <div
              :style="{
                width: getColumnList().length * 72 + 'px',
              }"
            >
              <p v-for="(item, i) in footerData" :key="i" class="col">
                {{ item }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ScrollerTable",
  props: {
    title: String,
    columns: Array,
    dataSource: Array,
    bodyHeight: {
      type: Number,
      default() {
        return 400;
      },
    },
  },
  data() {
    return {};
  },
  methods: {
    getColumnList(fixed) {
      return this.columns.filter((item) => item.fixed === fixed);
    },
    handleScroll(e) {
      const scrollLeft = e.target.scrollLeft;
      this.$refs.headerRight.scrollLeft = scrollLeft;
      this.$refs.bodyRight.scrollLeft = scrollLeft;
      this.$refs.footerRight.scrollLeft = scrollLeft;
    },
  },
  computed: {
    autoData() {
      return this.dataSource.reduce((result, item) => {
        return [...result, this.getColumnList().map((c) => item[c.field])];
      }, []);
    },
    fixedData() {
      return this.dataSource.reduce((result, item) => {
        return [...result, this.getColumnList(true).map((c) => item[c.field])];
      }, []);
    },
    footerData() {
      return this.getColumnList().map((c) => {
        return this.dataSource.reduce((result, item) => {
          result += Number.isInteger(item[c.field]) ? item[c.field] * 1 : 0;
          return result;
        }, 0);
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang='scss'>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.table {
  &__root {
    width: 100%;
    max-width: 640px;
    margin: auto;
    text-align: center;
  }
  &__title {
    line-height: 36px;
    height: 36px;
    background: #2378e1;
    color: #fff;
  }
  &__header {
    height: 72px;
    &-row {
      background: #3b90f4;
      color: #fff;
      display: flex;
      height: 36px;
    }
  }
  &__fixed {
    &-left {
      width: 144px;
      overflow: hidden;
    }
    &-right {
      flex: 1;
      overflow: hidden;
    }
  }
  &__body {
    overflow-y: auto;
    .row {
      height: 36px;
      &:nth-child(2n) {
        background: #f7f7f7;
      }
    }
  }
  &__footer {
    height: 36px;
    display: flex;
    background: #f7f7f7;
  }
}
.col {
  line-height: 36px;
  height: 36px;
  font-size: 12px;
  width: 72px;
  display: inline-block;
  overflow: hidden;
  // padding: 4px;
  // border-right: 1px solid #ccc;
}
</style>
