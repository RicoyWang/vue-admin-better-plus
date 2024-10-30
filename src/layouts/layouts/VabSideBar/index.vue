<template>
  <div class="side-bar-wrapper">
    <!-- 左侧一级菜单 -->
    <div class="top-menu">
      <div class="primary-menu">
        <vab-logo />
        <el-menu
          :active-text-color="variables['menu-color-active']"
          :background-color="variables['menu-background']"
          :default-active="activeMenu"
          :text-color="variables['menu-color']"
          mode="vertical"
          class="primary-menu-list"
          :unique-opened="uniqueOpened"
        >
          <template v-for="route in routes">
            <el-menu-item
              v-if="!route.hidden"
              :key="route.path"
              :index="route.path"
              @click="handlePrimaryMenuClick(route)"
            >
              <div class="primary-menu-item" v-if="route.meta">
                <vab-icon
                  v-if="route.meta.icon"
                  :icon="['fas', route.meta.icon]"
                  class="primary-menu-icon"
                />
                <span class="primary-menu-title">{{ route.meta.title }}</span>
              </div>
            </el-menu-item>
          </template>
        </el-menu>
      </div>
      <!-- 右侧二级菜单 -->
      <el-scrollbar v-if="currentRoute" class="secondary-menu">
        <el-menu
          :active-text-color="variables['menu-color-active']"
          :default-active="activeMenu"
          :text-color="variables['menu-color']"
          mode="vertical"
        >
          <template v-for="child in currentRoute.children">
            <vab-side-bar-item
              v-if="!child.hidden"
              :key="child.path"
              :item="child"
              :fullPath="currentRoute.path"
            />
          </template>
        </el-menu>
      </el-scrollbar>
    </div>
  </div>
</template>
<script>
  import variables from '@/styles/variables.scss'
  import { mapGetters } from 'vuex'
  import { defaultOopeneds, uniqueOpened } from '@/config'

  export default {
    name: 'VabSideBar',
    data() {
      return {
        uniqueOpened,
        currentRoute: null,
      }
    },
    computed: {
      ...mapGetters({
        collapse: 'settings/collapse',
        routes: 'routes/routes',
      }),
      defaultOpens() {
        if (this.collapse) {
        }
        return defaultOopeneds
      },
      activeMenu() {
        const route = this.$route
        const { meta, path } = route
        if (meta.activeMenu) {
          return meta.activeMenu
        }
        return path
      },
      variables() {
        return variables
      },
    },
    methods: {
      handlePrimaryMenuClick(route) {
        this.currentRoute = route
      },
    },
  }
</script>
<style lang="scss" scoped>
  @mixin active {
    &:hover {
      color: $base-color-white;
      background-color: $base-menu-background-active !important;
    }

    &.is-active {
      color: $base-color-white;
      background-color: $base-menu-background-active !important;
    }
  }

  .side-bar-container {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    z-index: $base-z-index;
    width: $base-left-menu-width;
    height: 100vh;
    overflow: hidden;
    background: $base-menu-background;
    box-shadow: 2px 0 6px rgba(0, 21, 41, 0.35);
    transition: width $base-transition-time;
    .secondary-menu {
      width: 300px;
    }
    &.is-collapse {
      width: $base-left-menu-width-min;
      border-right: 0;

      ::v-deep {
        .el-menu {
          transition: width $base-transition-time;
        }

        .el-menu--collapse {
          border-right: 0;

          .el-submenu__icon-arrow {
            right: 10px;
            margin-top: -3px;
          }

          .el-menu-item,
          .el-submenu {
            text-align: center;
          }
        }
      }
    }

    ::v-deep {
      .el-scrollbar__wrap {
        overflow-x: hidden;
      }

      .el-menu {
        border: 0;

        .vab-fas-icon {
          padding-right: 3px;
          font-size: $base-font-size-default;
          display: inline-block;
          width: 14px;
        }

        .vab-remix-icon {
          padding-right: 3px;
          font-size: $base-font-size-default + 2;
        }
      }

      .el-menu-item,
      .el-submenu__title {
        height: $base-menu-item-height;
        line-height: $base-menu-item-height;
        vertical-align: middle;
      }

      .el-menu-item {
        @include active;
      }
    }
  }
</style>
