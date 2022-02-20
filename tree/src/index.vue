<template>
  <div>
    <div @click="add">添加</div>
    <ul class="tree-ul">
      <treeList :renderTreeData="renderTreeData"
                :clickCheckBox="clickCheckBox"
                :openHideHandle="openHideHandle"></treeList>
    </ul>
  </div>
</template>

<script>
const bigData = require('./bigTree.json')
import treeList from './components/treeList.vue'
export default {
  data () {
    return {
      bigData: bigData.data,
      temp: {},
      renderTreeData: [],
      testData: {
        a: 1,
        b: {
          c: 2
        }
      }
    }
  },
  components: { treeList },
  created () {
    this.buildTree()
    console.log('testData', this.testData)
  },
  methods: {
    openHideHandle (id, isOpen) {
      let curObj = this.getTreeIdObj(id)
      curObj.open = isOpen
    },
    buildTree (list) {
      this.bigData.forEach(el => {
        this.$set(el, 'singleChecked', false)
        this.$set(el, 'allChecked', false)
      })
      console.time('time1')
      this.treeObj = {
        id: "20171208093858401-9581-744CD0990",
        pid: "20171208093854501-7DAB-BA64EE4EE",
        name: "银行股份有限公司党委",
        singleChecked: false,
        allChecked: false,
        level: 0,
        keyPosition: '',
        open: true,
        sonIds: this.bigData.filter(el => el.pid === '20171208093858401-9581-744CD0990').map(el => el.id).map(el => {
          return {
            singleChecked: false,
            allChecked: false,
            id: el
          }
        }),
        childrens: this.loopTree()
      }
      this.renderTreeData = [this.treeObj]
      this.flat()
    },
    flat () {
      let arr = []
      const flatArr = (obj) => {
        if (obj.childrens.length) {
          obj.childrens.forEach(el => {
            let curEl = JSON.parse(JSON.stringify(el))
            delete curEl.childrens
            arr.push(curEl)
            flatArr(el)
          })
        }
      }
      flatArr(this.treeObj)
      arr.unshift({
        id: "20171208093858401-9581-744CD0990",
        pid: "20171208093854501-7DAB-BA64EE4EE",
        name: "银行股份有限公司党委",
        singleChecked: false,
        allChecked: false,
        level: 0,
        keyPosition: '',
        sonIds: []
      })
      for (let index in arr) {
        this.temp[arr[index].id] = arr[index]
      }
    },
    loopTree () {
      const treeObj = (item, id, level = 1, pIndex = '', pid = ['20171208093858401-9581-744CD0990']) => {
        let curArr = this.bigData.filter(el => el.pid === id)
        return curArr.map((el, index) => {
          return {
            ...el,
            level: level,
            singleChecked: false,
            allChecked: false,
            sameLevelIds: curArr.map(el => el.id),
            allParentIds: pid,
            keyPosition: `${pIndex ? pIndex + ',' : ''}${index}`,
            open: true,
            sonIds: this.bigData.filter(item => item.pid === el.id).map(el => el.id).map(el => {
              return {
                singleChecked: false,
                allChecked: false,
                id: el
              }
            }),
            childrens: treeObj(el, id = el.id, level + 1, `${pIndex ? pIndex + ',' : ''}${index}`, [el.id, ...pid])
          }
        })
      }
      return treeObj({}, '20171208093858401-9581-744CD0990')
    },
    getTreeIdObj (id) {
      if (id === '20171208093854501-7DAB-BA64EE4EE') {
        return null
      }
      let curIdObj = this.temp[id]
      let mapObj = {}
      let arr = curIdObj.keyPosition === '' ? [] : curIdObj.keyPosition.split(',')
      if (!arr.length) {
        return this.treeObj
      }
      mapObj = arr.reduce((pre, cur) => {
        return pre.childrens[cur]
      }, this.treeObj)
      return mapObj
    },
    clickCheckBox (id, checkBoolean) {
      let arr = [id]
      this.setSelected(arr, checkBoolean)
    },
    add () {
      this.clearCheckTree()
      let arr = ['20190116154543365-604D-990BC893A', '20171208094101635-99F5-D25AA3FB2']
      this.setSelected(arr, true)
    },
    clearCheckTree () {
      Object.keys(this.temp).forEach(el => {
        let curObj = this.getTreeIdObj(el)
        curObj.singleChecked = false
        curObj.allChecked = false
        curObj.sonIds.forEach(el => {
          this.$set(el, 'singleChecked', false)
          this.$set(el, 'allChecked', false)
        })
      })
    },
    setSelected (arr, checkBoolean) {
      arr.forEach(el => {
        let mapObj = this.getTreeIdObj(el)
        this.$set(mapObj, 'singleChecked', checkBoolean)
        this.$set(mapObj, 'allChecked', checkBoolean)
        mapObj.sonIds.forEach(el => {
          el.singleChecked = checkBoolean
          el.allChecked = checkBoolean
        })
        if (!mapObj) return
        this.toSearchDown(mapObj, checkBoolean)
        this.toSearchUp(mapObj, checkBoolean)
      })
    },
    toSearchUp (mapObj, checkBoolean) { // 向上查找
      if (mapObj.id === '20171208093858401-9581-744CD0990') return
      let parentIds = [mapObj.id, ...mapObj.allParentIds]
      parentIds.reduce((pre, cur, index) => {
        if (pre === '') {
          return ''
        }
        let sonObj = this.getTreeIdObj(pre)
        let parentObj = this.getTreeIdObj(sonObj.pid)
        if (!parentObj) {
          return ''
        }
        let parentObjSonIdsItem = parentObj.sonIds[parentObj.sonIds.findIndex(el => el.id === pre)]
        if (!parentObjSonIdsItem) {
          return ''
        }
        let sonIdsLength = sonObj.sonIds.filter(el => el.singleChecked && el.allChecked).length // 全选的长度
        if (sonObj.sonIds.length === 0) { // son节点没有子节点，parent节点直接修改sonId中的子节点singleChecked和allChecked为checkBoolean
          parentObjSonIdsItem.singleChecked = checkBoolean
          parentObjSonIdsItem.allChecked = checkBoolean
        } else {
          parentObjSonIdsItem.singleChecked = sonObj.sonIds.filter(el => el.singleChecked).length !== 0
          parentObjSonIdsItem.allChecked = sonIdsLength === sonObj.sonIds.length
        }
        let parentIdsLength = parentObj.sonIds.filter(el => el.singleChecked && el.allChecked).length // 全选的长度
        this.$set(parentObj, 'singleChecked', parentObj.sonIds.filter(el => el.singleChecked).length !== 0)
        this.$set(parentObj, 'allChecked', parentIdsLength === parentObj.sonIds.length)
        return parentObj.id
      }, parentIds[0])
    },
    toSearchDown (mapObj, checkBoolean) { // 向下查找
      const loopSetChecked = (obj) => {
        obj.sonIds.forEach(el => {
          this.$set(el, 'singleChecked', checkBoolean)
          this.$set(el, 'allChecked', checkBoolean)
        })
        this.$set(obj, 'singleChecked', checkBoolean)
        this.$set(obj, 'allChecked', checkBoolean)
        if (obj.childrens.length) {
          obj.childrens.forEach(el => {
            loopSetChecked(el)
          })
        }
      }
      loopSetChecked(mapObj)
    },
    setCheckedId () {

    }
  }
}
</script>
<style>
* {
  padding: none;
  margin: none;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}
.tree-ul {
  width: 500px;
  text-align: left;
  height: 800px;
  overflow: auto;
}
</style>
