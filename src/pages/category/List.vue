<template>
  <q-page padding>
    <div class="row">
      <q-table
        :rows="categories"
        :columns="columns"
        row-key="id"
        class="col-12"
        :loading="loading"
    >
    <template v-slot:top>
      <span class="text-h6">
        Category
      </span>
      <q-space />
      <q-btn
        label="Add New"
        color="primary"
        icon="mdi-plus"
        dense
        :to="{ name: 'form-category' }"
      />
    </template>
    <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-x-sm">
          <q-btn icon="mdi-pencil-outline" color="info" dense size="sm" @click="handleEdit(props.row)">
            <q-tooltip>
              Edit
            </q-tooltip>
          </q-btn>
          <q-btn icon="mdi-delete-outline" color="negative" dense size="sm">
            <q-tooltip>
              Delete
            </q-tooltip>
          </q-btn>
        </q-td>
      </template>
    </q-table>
    </div>
  </q-page>
</template>

<script>

const columns = [
  { name: 'name', align: 'left', label: 'Name', field: 'name', sortable: true },
  { name: 'actions', align: 'right', label: 'Actions', field: 'actions', sortable: true }

]

import { defineComponent, ref, onMounted } from 'vue'
import useApi from 'src/composables/useApi'
import useNotify from 'src/composables/UseNotify'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'CategoryListPage',
  setup () {
    const categories = ref([])
    const loading = ref(true)
    const router = useRouter()

    const { list } = useApi()
    const { notifyError } = useNotify()

    const handleListCategories = async () => {
      try {
        loading.value = true
        categories.value = await list('category')
        loading.value = false
      } catch (error) {
        notifyError(error.message)
      }
    }

    const handleEdit = (category) => {
      router.push({ name: 'form-category', params: { id: category.id } })
    }

    onMounted(() => {
      handleListCategories()
    })

    return {
      columns,
      categories,
      loading,
      handleEdit
    }
  }
})
</script>