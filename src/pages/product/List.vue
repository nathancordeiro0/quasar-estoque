<template>
  <q-page padding>
    <div class="row">
      <q-table
        :rows="products"
        :columns="columnsProduct"
        row-key="id"
        class="col-12"
        :loading="loading"
    >
    <template v-slot:top>
      <span class="text-h6">
        Product
      </span>
      <q-space />
      <q-btn
        class="desktop-only"
        label="Add New"
        color="primary"
        icon="mdi-plus"
        dense
        :to="{ name: 'form-product' }"
      />
    </template>
    <template v-slot:body-cell-img_url="props">
        <q-td :props="props">
          <q-avatar v-if="props.row.img_url">
            <img :src="props.row.img_url">
          </q-avatar>
          <q-avatar v-else color="red" text-color="white" icon="mdi-image-off" />
        </q-td>
      </template>
    <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-x-sm">
          <q-btn icon="mdi-pencil-outline" color="info" dense size="sm" @click="handleEdit(props.row)">
            <q-tooltip>
              Edit
            </q-tooltip>
          </q-btn>
          <q-btn icon="mdi-delete-outline" color="negative" dense size="sm" @click="handleRemoveProduct(props.row)">
            <q-tooltip>
              Delete
            </q-tooltip>
          </q-btn>
        </q-td>
      </template>
    </q-table>
    </div>
    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <q-btn
        class="mobile-only"
        fab
        icon="mdi-plus"
        color="primary"
        :to="{ name: 'form-product' }"
      />
    </q-page-sticky>
  </q-page>
</template>

<script>

import { defineComponent, ref, onMounted } from 'vue'
import useApi from 'src/composables/useApi'
import useNotify from 'src/composables/UseNotify'
import { useRouter } from 'vue-router'
import { useQuasar } from 'quasar'
import { columnsProduct } from './table'

export default defineComponent({
  name: 'ProductListPage',
  setup () {
    const products = ref([])
    const loading = ref(true)
    const table = 'product'
    const router = useRouter()
    const $q = useQuasar()

    const { list, remove } = useApi()
    const { notifyError, notifySucess } = useNotify()

    const handleListProducts = async () => {
      try {
        loading.value = true
        products.value = await list(table)
        loading.value = false
      } catch (error) {
        notifyError(error.message)
      }
    }

    const handleEdit = (category) => {
      router.push({ name: 'form-product', params: { id: category.id } })
    }

    const handleRemoveProduct = async (category) => {
      try {
        $q.dialog({
          title: 'Confirm',
          message: `Do you really delete ${category.name} ?`,
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(table, category.id)
          notifySucess('Deleted Sucessfully!')
          handleListProducts()
        })
      } catch (error) {
        notifyError(error.message)
      }
    }

    onMounted(() => {
      handleListProducts()
    })

    return {
      columnsProduct,
      products,
      loading,
      handleEdit,
      handleRemoveProduct
    }
  }
})
</script>
