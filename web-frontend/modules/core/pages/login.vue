<template>
  <div>
    <div class="box__head">
      <h1 class="box__head-title">
        <nuxt-link :to="{ name: 'index' }">
          <img src="@baserow/modules/core/static/img/logo.svg" alt="" />
        </nuxt-link>
      </h1>
      <LangPicker />
    </div>
    <AuthLogin :invitation="invitation" @success="success">
      <ul class="action__links">
        <li v-if="settings.allow_new_signups">
          <nuxt-link :to="{ name: 'signup' }">
            {{ $t('action.signUp') }}
          </nuxt-link>
        </li>
        <li v-if="settings.allow_reset_password">
          <nuxt-link :to="{ name: 'forgot-password' }">
            {{ $t('login.forgotPassword') }}
          </nuxt-link>
        </li>
      </ul>
    </AuthLogin>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

import AuthLogin from '@baserow/modules/core/components/auth/AuthLogin'
import groupInvitationToken from '@baserow/modules/core/mixins/groupInvitationToken'
import LangPicker from '@baserow/modules/core/components/LangPicker'
import { isRelativeUrl } from '@baserow/modules/core/utils/url'

export default {
  components: { AuthLogin, LangPicker },
  layout: 'login',
  async asyncData({ redirect, route, app, store }) {
    if (store.getters['settings/get'].show_admin_signup_page === true) {
      redirect('signup')
    }

    return await groupInvitationToken.asyncData({ route, app })
  },
  head() {
    return {
      title: this.$t('login.title'),
      link: [
        {
          rel: 'canonical',
          href: this.$env.PUBLIC_WEB_FRONTEND_URL + this.$route.path,
        },
      ],
    }
  },
  computed: {
    ...mapGetters({
      settings: 'settings/get',
    }),
  },
  methods: {
    success() {
      const { original } = this.$route.query
      if (original && isRelativeUrl(original)) {
        this.$nuxt.$router.push(original)
      } else {
        this.$nuxt.$router.push({ name: 'dashboard' })
      }
    },
  },
}
</script>
