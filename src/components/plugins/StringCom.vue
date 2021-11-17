<template>
  <div>
    <template v-if="load">
      {{ data.prop + ':' }}
      <el-input
        v-model="returnVal"
        placeholder="请输入内容"
        style="width: 240px"
        @input="returnChange"
      />
    </template>
  </div>
</template>

<script>
export default {
  name: 'StringCom',
  props: {
    data: {
      type: Object,
      default: function() {
        return {}
      }
    }
  },
  data() {
    return {
      returnVal: '',
      load: false
    }
  },
  computed: {},
  mounted() {
    this.returnVal = this.data.default
    this.load = true
  },
  methods: {
    returnChange() {
      this.$emit('setRetrunData', this.data.propChain, this.returnVal)
    }
  }
}
</script>
