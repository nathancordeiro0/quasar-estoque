<template>
  <q-page padding>
    <q-form class="row justify-center" @submit.prevent="handleRegister">
        <p class="col-12 text-h5 text-center">Register</p>
        <div class="col-md-4 col-sn-6 col-xs-10 q-gutter-y-md">
          <q-input
            label="Name"
            v-model="form.name"
            lazy-rules
            :rules="[val => (val && val.length > 0) || 'Name is required!']"
            outlined
          />

          <q-input
            label="Email"
            v-model="form.email"
            lazy-rules
            :rules="[val => (val && val.length > 0) || 'Email is required!']"
            type="email"
            outlined
          />

          <q-input
            label="Password"
            v-model="form.password"
            lazy-rules
            :rules="[val => (val && val.length >= 6) || 'Password is required and 6 characters']"
            type="password"
            outlined
          />

          <div class="full-width q-pt-md q-gutter-y-sm">
            <q-btn
              label="Register"
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
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'RegisterPage',

  setup () {
    const router = useRouter()
    const { register } = useAuthUser()
    const { notifyError, notifySucess } = useNotify()

    const form = ref({
      name: '',
      email: '',
      password: ''
    })

    const handleRegister = async () => {
      try {
        await register(form.value)
        notifySucess('Sucess!')
        router.push({
          name: 'email-confirmation',
          query: { email: form.value.email }
        })
      } catch (error) {
        notifyError(error.message)
      }
    }

    return {
      form,
      handleRegister
    }
  }
})
</script>
