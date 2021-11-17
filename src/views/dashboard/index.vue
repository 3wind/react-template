<template>
  <div>
    <Plugins v-if="load" :childs="data" @setRetrunData="setRetrunData" />
  </div>
</template>

<script>
import Plugins from '@/components/plugins/index'
import { cloneDeep } from 'lodash'

export default {
  name: 'Dashboard',
  components: {
    Plugins
  },
  data() {
    return {
      data: {},
      load: false,
      returnData: {}
    }
  },
  mounted() {
    const sourceData = {
      fields: [
        {
          uri: {
            len_min: 1,
            required: true,
            default: '/ui',
            type: 'string'
          }
        },
        {
          whitelist: {
            elements: {
              is_regex: true,
              type: 'string'
            },
            default: [],
            type: 'array'
          }
        },
        {
          request: {
            fields: [
              {
                cookies: {
                  elements: {
                    type: 'string'
                  },
                  default: [],
                  type: 'array'
                }
              },
              {
                query: {
                  default: 'abc',
                  type: 'string'
                }
              }
            ],
            required: true,
            type: 'record'
          }
        }
      ]
    }
    const targetData = []
    const initResultData = {}

    this.formatInputAndResult(
      sourceData.fields,
      targetData,
      null,
      initResultData
    )

    console.log('-------InputAndResult------', targetData, initResultData)

    this.data = targetData
    this.returnData = initResultData
    this.load = true

    // 转换后的格式
    // this.data = {
    //   fields: [
    //     {
    //       prop: 'url',
    //       len_min: 1,
    //       required: true,
    //       default: '\/ui',
    //       type: 'StringCom'
    //     },
    //     {
    //       prop: 'whitelist',
    //       elements: {
    //         is_regex: true,
    //         type: 'string'
    //       },
    //       default: [],
    //       type: 'ArrayStringCom'
    //     },
    //     {
    //       prop: 'request',
    //       fields: [
    //         {
    //           prop: 'cookies',
    //           elements: {
    //             type: 'string'
    //           },
    //           default: [],
    //           type: 'ArrayStringCom'
    //         },
    //         {
    //           prop: 'query',
    //           default: 'abc',
    //           type: 'StringCom'
    //         }
    //       ],
    //       required: true,
    //       type: 'Plugins'
    //     }
    //   ]
    // }
  },
  methods: {
    // 格式化输入和输出
    formatInputAndResult(arr, targetArr, propChain, resultData) {
      for (let i = 0; i < arr.length; i++) {
        // 重新组织结构
        targetArr.push(
          cloneDeep({
            prop: Object.keys(arr[i])[0],
            ...arr[i][Object.keys(arr[i])[0]],
            propChain: propChain
              ? propChain + '^' + Object.keys(arr[i])[0]
              : Object.keys(arr[i])[0]
          })
        )
        // 转换类型
        this.handleType(targetArr[i].type, targetArr[i])

        // 设置返回值
        resultData[targetArr[i].prop] = targetArr[i].default

        // record递归， record没有默认值，也手动处理
        if (targetArr[i].type === 'Plugins') {
          targetArr[i].fields = []
          resultData[targetArr[i].prop] = {}

          this.formatInputAndResult(
            arr[i][Object.keys(arr[i])[0]].fields,
            targetArr[i].fields,
            targetArr[i].propChain,
            resultData[targetArr[i].prop]
          )
        }
      }
    },

    // 转换类型
    handleType(type, obj) {
      if (type === 'string') {
        obj.type = 'StringCom'
        return
      }

      if (type === 'array' && obj.elements.type === 'string') {
        obj.type = 'ArrayStringCom'
        return
      }

      if (type === 'record') {
        obj.type = 'Plugins'
        return
      }
    },
    // 返回值分层处理
    setDeepResultData(data, chains, length, value) {
      if (length === 1) {
        data[chains[chains.length - length]] = value
        return
      }
      while (length > 1) {
        this.setDeepResultData(
          data[chains[chains.length - length]],
          chains,
          --length,
          value
        )
      }
    },
    // 设置返回值
    setRetrunData(propChain, value) {
      const chains = propChain.split('^')
      if (chains.length === 1) {
        this.returnData = { ...this.returnData, [chains[0]]: value }
      } else {
        this.setDeepResultData(this.returnData, chains, chains.length, value)
      }
      console.log('------result------', this.returnData, chains, value)
    }
  }
}
</script>
