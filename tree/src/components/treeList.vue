<template>
  <ul>
    <li v-for="(item, index) in renderTreeData"
        class="treeLi"
        :data-id="item.id"
        :key="index">
      <span class="treeTitle">
        <span class="arrow"
              :class="{'arrow-add': !item.open, 'arrow-hidden': !item.sonIds.length}"
              @click="openHide(item)">

        </span>
        <span class="checkBox"
              :class="{'allChecked': item.singleChecked && item.allChecked, 'singleChecked': item.singleChecked && !item.allChecked}"
              @click="setCheckBox(item)">

        </span>
        <span class="treeNodeName">{{item.name}}</span>
      </span>
      <treeLsit v-if="item.childrens.length"
                :renderTreeData="item.childrens"
                :openHideHandle="openHideHandle"
                :id="item.id"
                class="childrensUl"
                :clickCheckBox="clickCheckBox"></treeLsit>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'treeLsit',
  data () {
    return {

    }
  },
  props: {
    renderTreeData: Array,
    clickCheckBox: Function,
    openHideHandle: Function
  },
  methods: {
    setCheckBox (item) {
      let checkBoolean = !(item.singleChecked && item.allChecked)
      this.clickCheckBox(item.id, checkBoolean)
    },
    openHide (item) {
      let domEl = document.getElementById(`${item.id}`)
      domEl.style.height = domEl.offsetHeight + 'px'
      if (item.open) {
        setTimeout(() => {
          domEl.style.height = '0px'
        })
      } else {
        setTimeout(() => {
          domEl.style.height = item.childrens.length * 22 + 'px'
          setTimeout(() => {
            domEl.style.height = 'auto'
          }, 301)
        })
      }
      this.openHideHandle(item.id, !item.open)
    }
  }
}
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.treeLi {
  transition: height ease 0.3s;
  overflow: hidden;
}
.childrensUl {
  transition: height ease 0.3s;
  overflow: hidden;
  height: auto;
}
.treeTitle {
  display: inline-block;
  height: 22px;
}
.hasChecked {
  color: blue;
}
.checkBox {
  position: relative;
  width: 14px;
  height: 14px;
  margin: 0 4px 0 0;
  border: 1px solid #d7dde4;
  border-radius: 2px;
  background: #fff;
  line-height: 22px;
  cursor: pointer;
  text-align: center;
  vertical-align: middle;
  display: inline-block;
  position: relative;
}
/* .allChecked {
  background: url("./../assets/打勾.png") no-repeat;
  background-size: 20px;
} */
.allChecked {
  border-color: #39f;
  background-color: #39f;
}
.allChecked:after {
  content: "";
  display: inline-block;
  position: absolute;
  top: 1px;
  left: 4px;
  width: 4px;
  height: 8px;
  transition: -webkit-transform 0.2s ease-in-out;
  -webkit-transition: -webkit-transform 0.2s ease-in-out;
  transition: transform 0.2s ease-in-out;
  transition: transform 0.2s ease-in-out, -webkit-transform 0.2s ease-in-out;
  /* -webkit-transform: rotate(0deg) scale(0); */
  /* transform: rotate(0deg) scale(0); */
  transform: rotate(45deg) scale(1);
  border-right: 2px solid #fff;
  border-bottom: 2px solid #fff;
}
.singleChecked {
  border-color: #39f;
  background-color: #39f;
}
.singleChecked:after {
  position: absolute;
  content: "";
  transition: -webkit-transform 0.2s ease-in-out;
  -webkit-transition: -webkit-transform 0.2s ease-in-out;
  transition: transform 0.2s ease-in-out;
  transition: transform 0.2s ease-in-out, -webkit-transform 0.2s ease-in-out;
  border-right: 2px solid #fff;
  border-bottom: 2px solid #fff;
  top: 5px;
  left: 2px;
  width: 10px;
  height: 1px;
  -webkit-transform: rotate(0deg) scale(1);
  transform: rotate(0deg) scale(1);
  border-right: 0;
}
.arrow {
  /* display: inline-block;
  width: 20px;
  height: 20px;
  background: url("./../assets/arrow-down.png") no-repeat;
  background-size: 20px;
  vertical-align: middle;
  cursor: pointer;
  transition: all ease 0.3s;
  visibility: visible; */
  position: relative;
  display: inline-block;
  line-height: 22px;
  height: 22px;
  width: 22px;
  cursor: pointer;
  text-align: center;
  vertical-align: middle;
  list-style-type: none;
  white-space: nowrap;
}
.arrow:after {
  -webkit-transform: rotate(90deg);
  transform: rotate(90deg);
  position: absolute;
  top: 5px;
  left: 5px;
  content: "";
  transition: -webkit-transform 0.3s ease;
  -webkit-transition: -webkit-transform 0.3s ease;
  transition: transform 0.3s ease;
  transition: transform 0.3s ease, -webkit-transform 0.3s ease;
  -webkit-transform: rotate(90deg);
  transform: rotate(90deg);
  -webkit-transform-origin: 25% 50%;
  transform-origin: 25% 50%;
  border: 6px solid;
  border-color: transparent transparent transparent #666;
}
.arrow-add:after {
  -webkit-transform: rotate(0deg);
  transform: rotate(0deg);
}
.arrow-hidden {
  visibility: hidden;
}
.treeNodeName {
  color: #555;
  list-style-type: none;
  white-space: nowrap;
  font-size: 14px;
  line-height: 22px;
}
</style>