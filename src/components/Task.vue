<template>
  <q-expansion-item
    dense
    class="full-width"
    expand-separator
  >
  <template v-slot:header>
    <slot>
    </slot>
    <div class="full-width flex items-center justify-start">
      <div v-if="hasEdit" class="q-pl-sm">{{propTask.title}}</div>
      <q-input @click.stop.prevent dense class="full-width q-pl-sm" v-else v-model="propTask.title" type="text"/>
    </div>
    <q-btn v-if="!propTask.hasDone" @click.stop.prevent="hasEdit = !hasEdit" round flat color="primary" :icon="hasEdit?'edit':'save'" />
  </template>
    <div class="q-pa-sm" v-if="hasEdit">
      {{propTask.desc}}
    </div>
    <q-input class="q-px-sm" dense v-else v-model="propTask.desc" type="text" />
  </q-expansion-item>
</template>

<script>
export default {
  name: 'EssentialLink',
  props: {
    task: {
      type: Object
    }
  },
  data () {
    return {
      hasEdit: true
    }
  },
  computed: {
    propTask: {
      get () {
        return this.task
      },
      set (value) {
        this.$emit('update:task', value)
      }
    }
  }
}
</script>
