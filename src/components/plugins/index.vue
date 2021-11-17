<template>
  <div>
    <template v-if="load">
      <div v-for="child in childs" :key="child.prop">
        <component
          :is="child.type"
          :childs="child.fields"
          :data="child"
          v-bind="$attrs"
          v-on="$listeners"
        />
        <br>
      </div>
    </template>
  </div>
</template>

<script>
import StringCom from './StringCom'
import ArrayStringCom from './ArrayStringCom'

export default {
  name: 'Plugins',
  components: {
    StringCom,
    ArrayStringCom
  },
  props: {
    childs: {
      type: Array,
      require: true,
      default: function() {
        return []
      }
    },
    data: {
      type: Object,
      default: function() {
        return {}
      }
    },
    type: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      load: false,
      retrunData: {}
    }
  },
  computed: {},
  mounted() {
    this.load = true
  },
  methods: {}
}
</script>
