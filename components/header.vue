<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon> {{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
            <v-divider />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />

      <v-toolbar-title v-text="title" />
      <v-spacer />
    </v-app-bar>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-home',
          title: 'ホーム',
          to: '/',
        },
        {
          icon: 'mdi-calendar-clock',
          title: 'プロジェクト作成',
          to: '/projects/makeProject',
        },
        {
          icon: 'mdi-message-question',
          title: 'プロジェクト一覧ページ',
          to: '/projects/projectList',
        },
        {
          icon: 'mdi-alarm',
          title: 'タスク追加ページ',
          to: '/projects/addTask',
        },
        {
          icon: 'mdi-alarm',
          title: 'タスクページ',
          to: '/projects/task',
        },
        {
          icon: 'mdi-account-cog-outline',
          title: 'プロフィール',
          to: '/projects/profile',
        },
        {
          icon: 'mdi-logout',
          title: 'ログアウト',
          to: '/logout',
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'スケジュールアプリ(仮称)',
    };
  },
  methods: {
    signOut() {
      this.$store
        .dispatch('signOut')
        .then(() => {
          this.$router.push({
            name: 'login',
          });
        })
        .catch((err) => {
          alert(err.message);
        });
    },
  },
};
</script>
