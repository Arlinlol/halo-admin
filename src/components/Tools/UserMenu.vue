<template>
  <div class="user-wrapper">
    <a
      :href="options.blog_url"
      target="_blank"
    >
      <span class="action">
        <a-icon type="link" />
      </span>
    </a>
    <a
      href="javascript:void(0)"
      @click="showOptionModal"
    >
      <span class="action">
        <a-icon type="setting" />
      </span>
    </a>
    <header-comment class="action" />
    <a-dropdown>
      <span
        class="action ant-dropdown-link user-dropdown-menu"
        v-if="user"
      >
        <a-avatar
          class="avatar"
          size="small"
          :src="user.avatar || '//cn.gravatar.com/avatar/?s=256&d=mm'"
        />
      </span>
      <a-menu
        slot="overlay"
        class="user-dropdown-menu-wrapper"
      >
        <a-menu-item key="0">
          <router-link :to="{ name: 'Profile' }">
            <a-icon type="user" />
            <span>个人资料</span>
          </router-link>
        </a-menu-item>
        <a-menu-divider />
        <a-menu-item key="1">
          <a
            href="javascript:;"
            @click="handleLogout"
          >
            <a-icon type="logout" />
            <span>退出登录</span>
          </a>
        </a-menu-item>
      </a-menu>
    </a-dropdown>
    <setting-drawer ref="drawer"></setting-drawer>
  </div>
</template>

<script>
import HeaderComment from './HeaderComment'
import SettingDrawer from '@/components/SettingDrawer/SettingDrawer'
import { mapActions, mapGetters } from 'vuex'
import optionApi from '@/api/option'

export default {
  name: 'UserMenu',
  components: {
    HeaderComment,
    SettingDrawer
  },
  data() {
    return {
      optionVisible: true,
      options: [],
      keys: ['blog_url']
    }
  },
  mounted() {
    this.optionVisible = this.$refs.drawer.visible
  },
  created() {
    this.loadOptions()
  },
  computed: {
    ...mapGetters(['user'])
  },
  methods: {
    ...mapActions(['logout']),
    handleLogout() {
      const that = this

      this.$confirm({
        title: '提示',
        content: '确定要注销登录吗 ?',
        onOk() {
          return that
            .logout({})
            .then(() => {
              window.location.reload()
            })
            .catch(err => {
              that.$message.error({
                title: '错误',
                description: err.message
              })
            })
        },
        onCancel() {}
      })
    },
    showOptionModal() {
      this.optionVisible = this.$refs.drawer.visible
      this.$refs.drawer.toggle()
    },
    loadOptions() {
      optionApi.listAll(this.keys).then(response => {
        this.options = response.data.data
      })
    }
  }
}
</script>

<style lang="less" scoped>
.avatar {
  margin-right: 0.3rem;
}
</style>
