<template>
  <div class="table__root">
    <div class="table__container">
      <div class="table__header">
        <div class="table__title">{{ title }}</div>
        <div class="table__header-row">
          <div
            class="table__fixed-left"
            :style="{ width: getColumnList(true, true).length * 72 + 'px' }"
          >
            <p
              v-for="item in getColumnList(true, true)"
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
                class="row"
                :style="{
                  width: getColumnList(false, true).length * 72 + 'px',
                  height: headerRowHeight + 'px',
                }"
              >
                <p
                  v-for="item in getColumnList(false, false)"
                  :key="item.field"
                  @click="
                    filterField === item.field
                      ? onFilterPikerShow(item.field)
                      : null
                  "
                  class="col"
                  :style="{
                    width:
                      (getChildList(item.children).length || 1) * 72 + 'px',
                  }"
                >
                  <template v-if="!item.children">
                    <span v-html="item.title" />
                    <i
                      class="filter__button"
                      v-if="filterField === item.field"
                    />
                  </template>
                  <template v-else>
                    <div>
                      <span v-html="item.title" class="col__children-title" />
                      <div
                        class="row"
                        :style="{
                          height:
                            dataLevel > 1
                              ? headerRowHeight - 24 + 'px'
                              : '36px',
                        }"
                      >
                        <p
                          v-for="c in item.children"
                          :key="c.field"
                          class="col"
                          :style="{
                            width:
                              (getChildList(c.children).length || 1) * 71 +
                              'px',
                          }"
                        >
                          <template v-if="!c.children">
                            <span v-html="c.title" />
                            <i
                              class="filter__button"
                              v-if="filterField === c.field"
                            />
                          </template>
                          <template v-else>
                            <div>
                              <span
                                v-html="c.title"
                                class="col__children-title"
                              />
                              <div class="row">
                                <p
                                  v-for="cc in c.children"
                                  :key="cc.field"
                                  class="col"
                                  :style="{ width: 71 + 'px' }"
                                >
                                  <span v-html="cc.title" />
                                  <i
                                    class="filter__button"
                                    v-if="filterField === cc.field"
                                  />
                                </p>
                              </div>
                            </div>
                          </template>
                        </p>
                      </div>
                    </div>
                  </template>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="table__body" :style="{ maxHeight: bodyHeight + 'px' }">
        <div :style="{ display: 'flex' }">
          <div
            class="table__fixed-left"
            :style="{ width: getColumnList(true, true).length * 72 + 'px' }"
          >
            <div
              v-for="(item, i) in fixedData"
              :key="i"
              class="row"
              :style="{ height: getRowMaxHeight(autoData[i])+'px' }"
            >
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
                  width: getColumnList(false, true).length * 72 + 'px',
                }"
              >
                <div
                  v-for="(item, i) in autoData"
                  :key="i"
                  class="row"
                  :style="{ height: getRowMaxHeight(item)+'px' }"
                >
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
                width: getColumnList(true, true).length * 72 + 'px',
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
              class="row"
              :style="{
                width: getColumnList(false, true).length * 72 + 'px',
              }"
            >
              <p
                v-for="(item, i) in footerData"
                :key="i"
                class="col"
                :style="{ width: typeof item === 'object' ? item.width : '' }"
              >
                <span v-html="typeof item === 'object' ? item.text : item" />
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
    dataLevel: {
      type: Number,
      default() {
        return 48;
      },
    },
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
    getChildList(root) {
      let result = [];
      loop(root);
      return result;
      function loop(data) {
        if (!data) {
          return [];
        }
        for (let item of data) {
          if (item.children) {
            loop(item.children);
          } else {
            result.push(item);
          }
        }
      }
    },
    getColumnList(fixed, isChildList) {
      let result = this.columns.filter(
        (item) => (item.fixed || false) === fixed
      );
      if (isChildList) {
        return this.getChildList(result);
      }
      return result;
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
    getRowMaxHeight(data) {
      let max = 36;
      for (let item of data) {
        console.log(item, "item");
        const matchData = (item || "").match(/<div height='(\d+)/);
        if (matchData && matchData[1] > max) {
          max = matchData[1];
        }
      }
      return max;
    },
  },
  computed: {
    autoData() {
      return this.getData().reduce((result, item, index) => {
        return [
          ...result,
          this.getColumnList(false, true).map((c) => {
            const value = item[c.field];
            return c.render ? c.render(value, item, index) : value;
          }),
        ];
      }, []);
    },
    fixedData() {
      return this.getData().reduce((result, item, index) => {
        return [
          ...result,
          this.getColumnList(true, true).map((c) => {
            const value = item[c.field];
            return c.render ? c.render(value, item, index) : value;
          }),
        ];
      }, []);
    },
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
    headerRowHeight() {
      if (this.dataLevel === 1) {
        return 48;
      }
      return 24 * (this.dataLevel - 1) + 36;
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
    }
  }
  &__fixed {
    &-left {
      overflow: hidden;
      .col {
        border-right: 1px solid #eee !important;
      }
    }
    &-right {
      flex: 1;
      overflow: hidden;
    }
  }
  &__body {
    overflow-y: auto;
    .row {
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
.row {
  height: 36px;
  width: 100%;
  & > .col {
    &:last-child {
      border-right: 0;
    }
  }
}
.col {
  height: 100%;
  font-size: 12px;
  width: 72px;
  display: flex;
  overflow: hidden;
  text-align: center;
  float: left;
  border-right: 1px solid #eee;
  &__children-title {
    display: block;
    height: 24px;
    line-height: 24px;
    border-bottom: 1px solid #eee;
  }
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
