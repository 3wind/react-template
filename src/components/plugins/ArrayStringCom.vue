<template>
  <div>
    <template v-if="load">
      <el-row
        v-for="(val, index) in returnVal"
        :key="index"
        style="width:480px"
      >
        {{ data.prop + ':' }}
        <el-input
          v-model="returnVal[index]"
          style="width:300px;"
          @input="returnChange"
        />
        <el-button
          v-if="index === 0"
          type="text"
          icon="el-icon-plus"
          style="padding: 10px 2px"
          circle
          @click="addItem"
        />
        <el-button
          v-if="returnVal.length !== 1"
          type="text"
          icon="el-icon-delete"
          style="padding: 10px 2px"
          circle
          @click.prevent="removeItem(index)"
        />
      </el-row>
    </template>
  </div>
</template>

<script>
export default {
  name: 'ArrayStringCom',
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
      returnVal: [''],
      load: false
    }
  },
  computed: {},
  mounted() {
    this.returnVal =
      Array.isArray(this.data.default) && this.data.default.length > 1
        ? this.data.default
        : ['']
    this.load = true
  },
  methods: {
    returnChange() {
      this.$emit('setRetrunData', this.data.propChain, this.returnVal)
    },
    addItem() {
      this.returnVal.push('')
    },
    removeItem(index) {
      this.returnVal.splice(index, 1)
    }
  }
}
</script>
