<template>
  <section data-group="management">
    <div data-management>
      <div data-management-item>
        <h2 data-icon>
          <i class="fa fa-circle fa-fw dot-not" aria-hidden="true"></i>
          부서 수정
        </h2>
        <groupdepartment data-component="tree" :treeEdit="true" :page="'user'"></groupdepartment>
      </div>
      <div data-management-item>
        <h2 data-icon>
          <i class="fa fa-circle fa-fw dot-now" aria-hidden="true"></i>
          부서원 정보
        </h2>
        <groupmembersrename data-component="member"></groupmembersrename>
      </div>
    </div>
    <!--<div data-management data-btn="confirm">-->
      <!--<el-button type="success" plain>닫기</el-button>-->
    <!--</div>-->
  </section>
</template>
<script>
import Groupdepartment from "./Group.department";
import Groupmembersrename from "./Group.members.rename";
import { mapGetters } from "vuex";
export default {
  name: "Grounpdetartmentset",
  extends: {},
  props: {
    //알파벳 순으로 정렬할 것.
  },
  data() {
    return {
      membersData: []
    };
  },
  computed: {
    ...mapGetters({ editUser: "fetchEditUser" })
  },
  components: {
    "groupdepartment":Groupdepartment,
    "groupmembersrename":Groupmembersrename
  },
  watch: {},
  methods: {
    saveStorage() {},
    resetStorage() {
      //EventBus.$emit("resetGroup");
      this.membersData = [];
    }
  },
  beforeCreate() {},
  created() {
    //    EventBus.$on("userview", data => {
    //      const apiUrl = "/api/admin/group/node/" + data;
    //      this.$http.get(apiUrl).then(result => {
    //        console.log(result.data.data);
    //        this.membersData = result.data.data;
    //      });
    //    });
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
.user-set-area {
  .user-set-list {
    border: 1px solid #ccc;
    border-radius: 5px;
    overflow: hidden;
  }
  .group-department,
  .group-members {
    .table-body-wrap {
      height: 400px;
    }
  }
}
</style>
