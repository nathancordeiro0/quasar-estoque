<template>
  <q-page padding>
    <q-form class="row justify-center" @submit.prevent="handlePasswordReset">
        <p class="col-12 text-h5 text-center">Reset Password</p>
        <div class="col-md-4 col-sn-6 col-xs-10 q-gutter-y-md">

          <q-input
            label="New Password"
            v-model="password"
            lazy-rules
            :rules="[val => (val && val.length >= 6) || 'Password is required!']"
            type="password"
          />

          <div class="full-width q-pt-md q-gutter-y-sm">
            <q-btn
              label="Send New Password"
              color="primary"
              class="full-width"
              rounded
              type="submit"
            />
          </div>
        </div>
      </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref } from 'vue'
import { useAuthUser } from 'src/composables/useAuthUser'
import useNotify from 'src/composables/UseNotify'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'ResetPasswordPage',

  setup () {
    const { resetPassword } = useAuthUser()
    const { notifyError, notifySucess } = useNotify()
    const route = useRoute()
    const router = useRouter()
    const token = route.query.token

    const password = ref('')

    const handlePasswordReset = async () => {
      try {
        await resetPassword(token, password.value)
        notifySucess('New Password Sent')
        router.push({ name: 'login' })
      } catch (error) {
        notifyError(error.message)
      }
    }

    return {
      password,
      handlePasswordReset
    }
  }
})

</script>
