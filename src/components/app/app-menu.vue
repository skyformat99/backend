<style lang="less">
.app-menu {
  .h-menu {
    font-size: 14px;
    .h-menu-li-selected {
      font-weight: 600;

      .h-menu-show:after {
        width: 4px;
      }
    }
    > li > .h-menu-show {
      font-size: 15px;
      .h-menu-show-icon {
        font-size: 20px;
      }
      .h-menu-show-desc {
        transition: opacity 0.1s cubic-bezier(0.645, 0.045, 0.355, 1), width 0.1s cubic-bezier(0.645, 0.045, 0.355, 1);
      }
    }
  }
  .h-menu.h-menu-size-collapse > .h-menu-li > .h-menu-show {
    padding-left: 24px;
    .h-menu-show-icon {
      font-size: 20px;
    }
  }
  .h-menu.h-menu-white {
    color: rgb(49, 58, 70);
  }
}
</style>
<template>
  <div class="app-menu">
    <appLogo></appLogo>
    <Menu :datas="menus" :inlineCollapsed="siderCollapsed" @click="trigger" ref="menu" :className="`h-menu-${theme}`"></Menu>
    <div class="app-menu-mask" @click="hideMenu"></div>

    <!-- 下面的图片引用是为了统计meedu的使用数量，无其他意义，可删除 -->
    <div style="display: none">
      <img src="https://meedu.vip/api/v1/stat?v=v4.0" />
    </div>
  </div>
</template>
<script>
import { mapState } from 'vuex';
import appLogo from './app-logo';

export default {
  props: {
    theme: String
  },
  data() {
    return {
      menus: []
    };
  },
  watch: {
    $route() {
      this.menuSelect();
    }
  },
  mounted() {
    this.init();
    const listener = G.addlistener('SYS_MENU_UPDATE', () => {
      this.init();
    });
    this.$once('hook:beforeDestroy', function () {
      G.removelistener(listener);
    });
  },
  computed: {
    ...mapState(['siderCollapsed'])
  },
  methods: {
    init() {
      R.User.menus().then(res => {
        this.menus = res.data.menus;
        this.menuSelect();
      });
      this.$nextTick(() => {
        this.menuSelect();
      });
    },
    menuSelect() {
      if (this.$route.name) {
        this.$refs.menu.select(this.$route.name);
      }
    },
    trigger(data) {
      if (data.children.length > 0) return;
      this.$router.push({ name: data.key });
    },
    hideMenu() {
      this.$store.commit('updateSiderCollapse', true);
    }
  },
  components: {
    appLogo
  }
};
</script>
