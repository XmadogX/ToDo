<template>
  <q-card flat>
    <q-slide-transition>
      <div v-show="visible">
        <q-card-actions align="between">
          <q-btn round flat @click="visible = !visible" color="primary" icon="keyboard_backspace" />
          <div class="text-h6 text-weight-light">New task</div>
        </q-card-actions>
        <q-card-actions vertical align="center">
          <q-form class="full-width" greedy ref="form">
            <q-input v-model="newTask.title" :rules="[val => !!val || 'Field is required']" type="text" label="Title" />
            <q-input v-model="newTask.desc" type="text" label="Deescription" />
          </q-form>
          <q-btn class="full-width q-mt-md" rounded flat @click="addTask" color="primary" label="+ Add task" />
        </q-card-actions>
      </div>
    </q-slide-transition>
    <q-slide-transition>
      <div v-show="!visible">
        <q-card-actions align="between">
          <q-btn rounded flat @click="visible = !visible" color="primary" label="+ Add task" />
          <div class="text-h6 text-weight-light">My tasks</div>
        </q-card-actions>
          <q-table
          class="full-width"
          dense
          :data="calcTasks"
          :columns="columns"
          :pagination.sync="pagination"
          hide-header
          grid
        >
          <template v-slot:item="props">
            <task :class="{'bg-green-1':tasks[props.key].hasDone}" :task.sync="tasks[props.key]">
              <q-btn @click.stop.prevent="deleteTask(props.key)" round flat color="red" icon="delete_forever" />
              <q-btn v-if="!tasks[props.key].hasDone" @click.stop.prevent="checkTask(props.key)" round flat color="green" icon="done_outline" />
            </task>
          </template>
        </q-table>
      </div>
    </q-slide-transition>
  </q-card>
</template>

<script>
import Task from '../components/Task'

export default {
  name: 'ToDoIndex',
  components: {
    Task
  },
  data () {
    return {
      pagination: {
        sortBy: 'title',
        descending: true,
        rowsPerPage: 0
      },
      newTask: {
        title: null,
        desc: null,
        hasDone: false
      },
      visible: false,
      tasks: [
      ],
      columns: [
        {
          name: 'title',
          align: 'left',
          field: row => row.title,
          format: val => `${val}`,
          required: true,
          sortable: true
        }
      ]
    }
  },
  created () {
    this.tasks = JSON.parse(localStorage.tasks)
  },
  watch: {
    tasks: {
      handler (value) {
        localStorage.setItem('tasks', JSON.stringify(value))
      },
      deep: true
    }
  },
  computed: {
    calcTasks () {
      return this.tasks.map((task, index) => ({ id: index, ...task }))
    }
  },
  methods: {
    addTask () {
      this.$refs.form.validate().then(resp => {
        if (resp) {
          this.tasks.push({ ...this.newTask })
          this.newTask.title = null
          this.newTask.desc = null
          this.visible = false
        }
      })
    },
    checkTask (index) {
      this.tasks[index].hasDone = true
    },
    deleteTask (index) {
      this.$delete(this.tasks, index)
    }
  }
}
</script>
