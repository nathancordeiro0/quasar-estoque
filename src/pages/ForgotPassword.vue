<template>
  <q-page padding>
    <q-form class="row justify-center" @submit.prevent="handleForgotPassword">
        <p class="col-12 text-h5 text-center">Reset Password</p>
        <div class="col-md-4 col-sn-6 col-xs-10 q-gutter-y-md">

          <q-input
            label="Email"
            v-model="email"
            outlined
            lazy-rules
            :rules="[val => (val && val.length > 0) || 'Password is required!']"
            type="email"
          />

          <div class="full-width q-pt-md q-gutter-y-sm">
            <q-btn
              label="Send Reset Email"
              color="primary"
              class="full-width"
              outlined
              rounded
              type="submit"
            />
            <q-btn
              label="Back"
              color="dark"
              class="full-width"
              flat
              :to="{ name: 'login' }"
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

export default defineComponent({
  name: 'PageForgotPassword',

  setup () {
    const { sendPasswordRestEmail } = useAuthUser()
    const { notifyError, notifySucess } = useNotify()

    const email = ref('')

    const handleForgotPassword = async () => {
      try {
        await sendPasswordRestEmail(email.value)
        notifySucess(`Password reset email send to: ${email.value}`)
      } catch (error) {
        notifyError(error.message)
      }
    }

    return {
      email,
      handleForgotPassword
    }
  }
})
</script>
