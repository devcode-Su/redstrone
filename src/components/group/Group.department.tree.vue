<template>
  <li :class="[{'item-root' : isRootItem },{'off' : !isOpen && isRootItem },{'end-list' : !model.children }]">
    <div @click.stop="setOpen(page)" :class="{on: model.children && isOpen}">
      <span>
        <i v-if="isFolder" class="fa fa-caret-right" :class="{'rotate' : isOpen}">
        </i>
        <span v-if="rename" class="label">
          {{model.name}}
        </span>
        <input v-else type="text" v-model="model.name" @blur="doneEdit(model)" @keyup.enter="doneEdit(model)" @keyup.esc="cancelEdit" ref="part">
      </span>
      <span v-if="treeEdit" class="edit-wrap">
        <button class="icon-btn" @click.stop="renameTo(model)">
          <i class="fa fa-pencil" aria-hidden="true"></i>
        </button>
        <button v-if="!isFolder" class="icon-btn" @click.stop="changeType">
          <i class="fa fa-folder-open-o" aria-hidden="true"></i>
        </button>
        <button v-if="isFolder" class="icon-btn" @click.stop="addTree">
          <i class="fa fa-plus" aria-hidden="true"></i>
        </button>
        <button v-if="!isFolder" class="icon-btn" @click.stop="removeTree(model)">
          <i class="fa fa-minus" aria-hidden="true"></i>
        </button>
      </span>
    </div>
    <transition @enter="enter" @leave="leave">
      <ul v-show="isOpen" v-if="model.children">
        <template-grouptree v-for="(model, i) in model.children" :key="model.id" :model="model" :index="i" :treeEdit="treeEdit" :page="page">
        </template-grouptree>
      </ul>
    </transition>
  </li>
</template>
<script>
import Velocity from "velocity-animate";
import Constant from "@/constant";
export default {
  name: "TemplateGrouptree",
  extends: {},
  props: {
    //알파벳 순으로 정렬할 것.
    model: Array | Object,
    index: Number,
    treeEdit: {
      type: Boolean,
      default: false
    },
    page: String
  },
  data() {
    return {
      isOpen: false,
      edited: null,
      rename: true
    };
  },
  computed: {
    isRootItem() {
      return this.model.name === "undefined" || this.model.name === "전사";
    },
    isFolder() {
      return this.model.children && this.model.children.length;
    }
  },
  components: {},
  watch: {},
  methods: {
    setOpen(page) {
      //console.log(page);
      this.isOpen = !this.isOpen;
      if (this.model.name !== "전사" && this.$children.length) {
        if (this.isOpen) this.$emit("is-open", this.$parent.$children);
      }
      if (page === "from") {
        this.$store.commit(Constant.EDITGROUP_CODE_FROM, this.model.dept_code);
      } else if (page === "to") {
        this.$store.commit(Constant.EDITGROUP_CODE_TO, this.model.dept_code);
      } else if (page === "user") {
        this.$store.commit(Constant.EDITUSER_CODE, this.model);
      } else {
        this.$store.dispatch(Constant.GLOBAL_RANGECODE, this.model);
        this.$bus.$emit("update-group");
      }
    },
    enter(el, done) {
      Velocity(
        el,
        "slideDown",
        {
          duration: 200,
          easing: "easeInBack"
        },
        { complete: done }
      );
    },
    leave(el, done) {
      Velocity(
        el,
        "slideUp",
        {
          duration: 200,
          easing: "easeInBack"
        },
        { complete: done }
      );
    },
    renameTo(model) {
      this.rename = false;
      this.edited = model;
      setTimeout(() => {
        this.$refs.part.focus();
      });
    },
    doneEdit(model) {
      if (!this.edited) return;
      this.edited = null;
      model.name = model.name.trim();
      if (!model.name) this.removeModel(model);
      this.rename = true;
    },
    addTree() {
      this.isOpen = true;
      this.$prompt("추가할 부서의 이름을 입력하세요.", "부서추가", {
        confirmButtonText: "추가",
        cancelButtonText: "취소"
      })
        .then(value => {
          this.$message({
            showClose: true,
            type: "success",
            message: "추가될 부서명 : " + value.value,
            duration: 2000
          });
          console.log(this.model);
          this.$store.dispatch(Constant.ADD_GROUP, {
            pcode: this.model.dept_code,
            name: value.value
          });
        })
        .catch(() => {
          this.$message({
            showClose: true,
            type: "info",
            message: "부서추가를 취소합니다.",
            duration: 2000
          });
        });
      // var name = prompt("추가될 부서의 이름을 입력하세요.","");
      // alert(name)

      //this.$store.dispatch(Constant.ADD_GROUP, )
      // this.model.children.push({
      //   name: "이름을 수정하세요."
      // });
    },
    removeTree(model) {
      console.log(model);
      const url = "/api/admin/group/" + model.dept_code;
      this.$http.delete(url);
      const tree = this.$parent.model.children;
      tree.splice(tree.indexOf(model), 1);

    },
    changeType() {
      if (!this.isFolder) {
        this.$set(this.model, "children", [{ name: "이름을 수정하세요." }]);
      }
    },
    cancelEdit() {
      this.rename = true;
      this.edited = null;
    }
  },
  beforeCreate() {},
  created() {
    this.$on("is-open", item => {
      //console.log("is-open event received");
      for (var i = 0; i < item.length; i++) {
        if (this.index !== i) {
          //this.isOpen = false;
          item[i].isOpen = false;
        }
      }
    });
  },
  beforeMounted() {},
  mounted() {},
  beforeUpdate() {},
  updated() {},
  activated() {},
  deactivated() {},
  beforeDestroy() {},
  destroyed() {}
};
</script>
<style lang='scss' scoped>
@import "~styles/variables";
</style>
