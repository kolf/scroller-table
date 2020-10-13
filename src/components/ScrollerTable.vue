<template>
  <div class="table__root">
    <div class="table__container">
      <div class="table__header">
        <div class="table__title">{{ title }}</div>
        <div class="table__header-row">
          <div
            class="table__fixed-left"
            :style="{ width: getColumnList(true).length * 72 + 'px' }"
          >
            <p
              v-for="item in getColumnList(true)"
              :key="item.field"
              @click="
                filterField === item.field
                  ? onFilterPikerShow(item.field)
                  : null
              "
              class="col"
            >
              <span v-html="item.title" />
              <i class="filter__button" v-if="filterField === item.field" />
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
                  @click="
                    filterField === item.field
                      ? onFilterPikerShow(item.field)
                      : null
                  "
                  class="col"
                >
                  <span v-html="item.title" class="table__header-row--title" />
                  <i class="filter__button" v-if="filterField === item.field" />
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="table__body" :style="{ maxHeight: bodyHeight + 'px' }">
        <div :style="{ height: fixedData.length * 36 + 'px', display: 'flex' }">
          <div
            class="table__fixed-left"
            :style="{ width: getColumnList(true).length * 72 + 'px' }"
          >
            <div v-for="(item, i) in fixedData" :key="i" class="row">
              <p v-for="(f, j) in item" :key="j" class="col">
                <span v-html="item[j]" />
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
                    <span v-html="item[j]" />
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
            <p
              class="col"
              :style="{
                width: getColumnList(true).length * 72 + 'px',
              }"
            >
              <span>合计</span>
            </p>
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
                <span v-html="item" />
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <van-picker
      v-if="showFilterPiker"
      show-toolbar
      :columns="filterOptions"
      @confirm="confirmFilterPiker"
      @cancel="hideFilterPiker"
      class="picker__root"
    />
  </div>
</template>

<script>
export default {
  name: "ScrollerTable",
  props: {
    title: String,
    columns: Array,
    dataSource: Array,
    footerData: Array,
    filterField: String,
    bodyHeight: {
      type: Number,
      default() {
        return 400;
      },
    },
  },
  data() {
    return {
      filterValue: "",
      showFilterPiker: false,
    };
  },
  methods: {
    getData() {
      if (!this.filterValue || this.filterValue === "全部") {
        return this.dataSource;
      }
      return this.dataSource.filter((item) => {
        return item[this.filterField] === this.filterValue;
      });
    },
    getColumnList(fixed) {
      return this.columns.filter((item) => item.fixed === fixed);
    },
    handleScroll(e) {
      const scrollLeft = e.target.scrollLeft;
      this.$refs.headerRight.scrollLeft = scrollLeft;
      this.$refs.bodyRight.scrollLeft = scrollLeft;
      this.$refs.footerRight.scrollLeft = scrollLeft;
    },
    onFilterPikerShow() {
      this.showFilterPiker = true;
    },
    hideFilterPiker() {
      this.showFilterPiker = false;
    },
    confirmFilterPiker(value) {
      this.filterValue = value;
      this.hideFilterPiker();
      this.$emit("filter", this.filterField, this.filterValue);
    },
  },
  computed: {
    autoData() {
      return this.getData().reduce((result, item) => {
        return [
          ...result,
          this.getColumnList().map((c) => {
            const value = item[c.field];
            return c.render ? c.render(value, item) : value;
          }),
        ];
      }, []);
    },
    fixedData() {
      return this.getData().reduce((result, item) => {
        return [
          ...result,
          this.getColumnList(true).map((c) => {
            const value = item[c.field];
            return c.render ? c.render(value, item) : value;
          }),
        ];
      }, []);
    },
    // footerData() {
    //   return this.getColumnList().map((c) => {
    //     return this.getData().reduce((result, item) => {
    //       result += Number.isInteger(item[c.field]) ? item[c.field] * 1 : 0;
    //       return result;
    //     }, 0);
    //   });
    // },
    filterOptions() {
      return this.dataSource.reduce(
        (result, item) => {
          if (!result.includes(item[this.filterField])) {
            result.push(item[this.filterField]);
          }
          return result;
        },
        ["全部"]
      );
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
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
    &-row {
      background: #3b90f4;
      color: #fff;
      display: flex;
      .col {
        height: 48px;
      }
    }
  }
  &__fixed {
    &-left {
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
  display: flex;
  overflow: hidden;
  text-align: center;
  float: left;
  border-right: 1px solid #eee;
  & > * {
    margin: auto;
    line-height: 1.2;
  }
}
.picker {
  &__root {
    position: fixed;
    width: 100%;
    bottom: 0;
  }
}
.filter__button {
  position: relative;
  left: -6px;
  top: -2px;
  border: 3px solid;
  border-color: transparent transparent #fff #fff;
  transform: rotate(-45deg);
  opacity: 0.8;
}
</style>
