<template>
  <nav data-layout="Navigation">
    <ul>
      <li data-nav-list="category" data-tooltip-wrap v-for="(category,i) in nav" :key="category.i" :class="{on : i === selectNum}" @click="categorySelect(i)">
        <span data-icon="nav">
          <i class="material-icons" aria-hidden="true">{{category.icon}}</i>
          <span data-tooltip="category">{{category.title}}</span>
        </span>
        <ul data-nav-page>
          <li data-nav-page-list v-for="(page,i) in category.page" :key="page.i">
            <router-link :to="'/Redstone/'+page.path">
              {{page.title}}
            </router-link>
          </li>
        </ul>
      </li>
    </ul>
  </nav>
</template>
<script>
  //import Constant from "@/constant";
import locationCheckMixin from "../mixins/location.check.mixin";
export default {
  name: "RedNavigation",
  extends: {},
  props: {
    //알파벳 순으로 정렬할 것.
  },
  data() {
    return {
      nav: [],
      selectNum: ""
    };
  },
  computed: {},
  components: {},
  watch: {},
  methods: {
    categorySelect(select) {
      if (this.selectNum !== select) {
        this.selectNum = select;
      }
//      this.$emit("selectedBoolean", 0 !== select);
    }
  },
  beforeCreate() {},
  created() {
    const apiUrl = "/static/data/navigation.json";
    this.$http.get(apiUrl).then(result => {
      this.nav = result.data;
    });
  },
  beforeMounted() {},
  mounted() {},
  beforeUpdate() {},
  updated() {
    let pageUrl = location.href;
    let i = this.nav.length;
    while (i--) {
      let u = pageUrl.match(this.nav[i].name);
      if (u !== null) this.selectNum = i;
    }
  },
  activated() {},
  deactivated() {},
  beforeDestroy() {},
  destroyed() {},
  mixins: [locationCheckMixin]
};
</script>
<style lang='scss' scoped>
@import "~styles/variables";

[data-nav-list="category"] {
  position: relative;
  height: 50px;
  &:hover {
    [data-icon] {
      top: -7px;
      right: -7px;
      bottom: -7px;
      z-index: 5;
      border: 1px solid #1a222f;
      box-shadow: 2px 3px rgba(26, 34, 47, 0.4);
    }
    ul {
      width: 150px;
      opacity: 1;
    }
  }
  &:hover,
  &.on {
    i {
      color: color(highlight-color);
    }
  }
}
[data-icon] {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: color(default);
  transition: all 0.15s ease;
}

[data-nav-page] {
  width: 0;
  position: absolute;
  top: 5px;
  left: 50px;
  z-index: 5;
  opacity: 0;
  overflow: hidden;
  transition: width, 0.3s ease;
}

[data-nav-page-list] {
  display: block;
  margin: 0 0 5px 15px;
  padding: 0;
  background-color: rgba(26, 34, 47, 0.6);
  border-radius: 2px;
  box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 3px 1px -2px rgba(0, 0, 0, 0.2),
    0 1px 5px 0 rgba(0, 0, 0, 0.12);
  a {
    display: block;
    width: 100%;
    height: 32px;
    min-height: 32px;
    line-height: 32px;
    padding: 0;
    font-size: 14px;
    text-align: center;
    color: color(white);
  }
  &.on,
  &:hover {
    background-color: rgba(26, 34, 47, 0.9);
  }
}
</style>
