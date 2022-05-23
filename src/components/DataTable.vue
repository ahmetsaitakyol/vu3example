<template>
  <div class="q-pa-md">
    <q-table
      :rows="rows"
      :columns="columns"
      row-key="id"
      key="id"
      binary-state-sort
    >
      <template v-slot:top>
        <div>Personal</div>
        <q-space />
        <q-btn label="Add" color="primary" @click="prompt = true" />
      </template>
      <template v-slot:body-cell-action="props">
        <q-td :props="props">
          <div>
            <q-btn icon="visibility" size="sm" @click="view(props.row.id)"></q-btn>
            <q-btn icon="edit" size="sm" @click="edit(props.row)"></q-btn>
            <q-btn icon="delete" size="sm" @click="deleteRow(props.row.id)"></q-btn>
          </div>
        </q-td>
      </template>
    </q-table>
    <q-dialog v-model="prompt" persistent @hide="hide">
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Your Informations</div>
        </q-card-section>
        <q-card-section class="q-pt-none">
          <q-input dense placeholder="Name" v-model="personal.name" autofocus />
        </q-card-section>
        <q-card-section class="q-pt-none">
          <q-input dense placeholder="Age" v-model="personal.age" />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancel" v-close-popup />
          <q-btn v-if="!isEdit" flat label="Add" @click="add()" v-close-popup />
          <q-btn v-else flat label="Edit" @click="saveEdit()" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
import { useRouter } from 'vue-router'
import { ref } from 'vue'
import { useQuasar } from 'quasar'


const columns = [
  {
    name: 'name',
    label: 'Name',
    align: 'left',
    field: 'name',
    sortable: true
  },
  { name: 'age', align: 'center', label: 'Age', field: 'age', sortable: true },
  { name: 'action', align: 'center', label: 'Action', field: 'action', sortable: false }
]

const rows = [
  {
    id: 1,
    name: 'John Doe 1',
    age: 23,
  },
  {
    id: 2,
    name: 'John Doe 2',
    age: 25,
  },
]

export default {
  setup () {
    const tmpRows = ref(rows)
    let isEdit = ref(false)
    let prompt = ref(false)
    let personal = ref([])
    const router = useRouter()
    const q = useQuasar()

    function add() {
      const lastId = Math.max(...tmpRows.value.map(i => i.id))
      this.personal.id = Number(lastId + 1)
      tmpRows.value.push({...this.personal})
    }
    function view(id) {
      router.push({name: 'Detail', params: {id: id}})
    }
    function edit(row) {
      isEdit.value = true
      personal.value = row
      prompt.value = true
    }
    function deleteRow (id) {
      q.dialog({
        title: 'Sil',
        message: 'Silmek istediğinize emin misiniz?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        const index = tmpRows.value.findIndex(i => i.id === id)
        tmpRows.value.splice(index, 1)
      })
    }
    function saveEdit() {
      tmpRows.value.map(item => {
        if (personal.value.id === item.id) {
          item = personal.value
        }
      })
      prompt.value = false
    }
    function hide () {
      isEdit.value = false
      personal.value = []
    }
    return {
      columns,
      rows: tmpRows,
      add,
      edit,
      view,
      deleteRow,
      hide,
      saveEdit,
      prompt: prompt,
      personal,
      isEdit
    }
  }
}
</script>
