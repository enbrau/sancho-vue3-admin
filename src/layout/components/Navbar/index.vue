<template>
  <div class="navbar">
    <hamburger
      id="hamburger-container"
      :is-active="sidebar"
      class="hamburger-container"
      @toggleClick="toggleSideBar"
    />
    <breadcrumb
      id="breadcrumb-container"
      class="breadcrumb-container"
    />
    <div id="global-sub-function-slot" style="display: inline-block; font-size: 14px; line-height: 50px; margin-left: 8px;"></div>
    <div class="right-menu">
      <template v-if="device!=='mobile'">
        <component
          :is="$key"
          v-for="(tool, $key) in tools"
          :key="$key"
        />
      </template>
      <el-dropdown
        class="avatar-container right-menu-item hover-effect"
        trigger="click"
      >
        <div class="avatar-wrapper">
          <img
            v-if="avatar"
            :src="avatar+'?imageView2/1/w/80/h/80'"
            class="user-avatar"
          >
          <el-avatar
            v-else
            :class="'user-avatar' + (isCancelImpersonateEnabled ? ' impersonate' : '')"
          >
            <svg-icon
              v-if="!isCancelImpersonateEnabled"
              icon-class="male"
            />
            <svg-icon
              v-if="isCancelImpersonateEnabled"
              icon-class="spy"
            />
          </el-avatar>
          <i class="el-icon-caret-bottom" />
        </div>
        <template #dropdown>
          <el-dropdown-menu class="sancho-user-menu">
            <component :is="$key" v-for="(action, $key) in actions" :key="$key" />
            <el-divider style="margin: 0px;"></el-divider>
            <el-dropdown-item>
              <span style="display:block;" @click="logout">
                <svg-icon icon-class="exit" />
                {{ $t('common.quit') }}
              </span>
            </el-dropdown-item>
          </el-dropdown-menu>
        </template>
      </el-dropdown>
    </div>
  </div>
</template>

<script>
import { delCookie } from '@/utils/cookie'
import { mapState } from 'vuex'
import Breadcrumb from './Breadcrumb'
import Hamburger from './Hamburger'
import tools from './tools'
import actions from './actions'

export default {
  components: { Breadcrumb, Hamburger, ...tools, ...actions },
  data() {
    return {
      tools,
      actions,
      avatar: null
    }
  },
  computed: {
    ...mapState({
      sidebar: state => state.app.sidebar,
      device: state => state.user.device
    }),
    isCancelImpersonateEnabled() {
      return this.$settings.enableImpersonate || false
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.$store.dispatch('app/navbarReady')
    })
  },
  methods: {
    toggleSideBar() {
      this.$store.dispatch('app/toggleSideBar')
    },
    logout() {
      delCookie(this.$settings.security.token_key.cookie)
      window.location = `${window.location.protocol}//${window.location.host}${window.location.pathname}`
    }
  }
}
</script>

<style lang="scss">
@import "~@/styles/mixin.scss";
@import "~@/styles/variables.scss";

.sancho-user-menu {
  .el-dropdown-menu__item {
    padding: 0 15px;
    text-align: left;
  }
}

.navbar {
  height: 50px;
  overflow: hidden;
  position: relative;
  background: $navBarBg;
  // box-shadow: 0 1px 4px rgba(0,21,41,.08);

  .hamburger-container {
    line-height: 46px;
    height: 100%;
    float: left;
    cursor: pointer;
    transition: background .3s;
    -webkit-tap-highlight-color:transparent;

    &:hover {
      background: rgba(0, 0, 0, .025)
    }
  }

  .breadcrumb-container {
    float: left;
  }

  .errLog-container {
    display: inline-block;
    vertical-align: top;
  }

  .right-menu {
    float: right;
    height: 100%;
    line-height: 50px;

    &:focus {
      outline: none;
    }

    .right-menu-item {
      display: inline-block;
      padding: 0 10px;
      height: 100%;
      font-size: 18px;
      color: #5a5e66;
      line-height: 50px;
      vertical-align: text-bottom;

      &.hover-effect {
        cursor: pointer;
        transition: background .3s;

        &:hover {
          background: rgba(0, 0, 0, .025)
        }
      }
    }

    .right-menu-divider {
      display: inline-block;
      height: 60%;
      vertical-align: super;
    }

    .avatar-container {
      margin-right: 30px;

      .avatar-wrapper {
        margin-top: 5px;
        position: relative;

        .user-avatar {
          cursor: pointer;
          width: 40px;
          height: 40px;
          border-radius: 10px;

          &.impersonate {
            background-color: #ff4949;
          }
        }

        .el-icon-caret-bottom {
          cursor: pointer;
          position: absolute;
          right: -20px;
          top: 25px;
          font-size: 12px;
        }
      }
    }
  }
}
</style>
