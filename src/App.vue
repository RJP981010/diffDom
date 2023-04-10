<template>
  <div id="app">
    <!-- <nav>
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </nav>
    <router-view/> -->
    <p>你的</p>
    <!-- <span
      v-for="(item,index) in resultStr"
      :key="index"
      :class="item.added ? 'green' :item.removed ? 'red' : 'grey'">{{ item.value }}</span> -->
    <span class="grey">{{beforeStr}}</span>
    <hr/>
    <p>他们的</p>
    <span class="grey">{{affterStr}}</span>
    <hr/>
    <p>冲突</p>
    <template v-for="(item,index) in resultStr">
      <el-popconfirm
        @confirm="confirm(item)"
        :key="index + 'a'"
        title="这是一段内容确定删除吗？"
      >
        <span
          slot="reference"
          :class="item.added ? 'green' :item.removed ? 'red' : 'grey'">
          {{ item.value }}
        </span>
      </el-popconfirm>
    </template>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        resultStr: [],
        beforeStr: '123456789',
        affterStr: '122347899'
      }
    },
    methods: {
      confirm(data) {
        let modifiedText = ''
        this.resultStr.forEach(item => {
          if(!item.removed && !item.added) {
            modifiedText += item.value;
          } else if (data.id == item.id || item.used) {
            // 添加的内容
            modifiedText += item.added ? item.value : '';
          } else if(data.id !== item.id && item.removed) {
            modifiedText += item.value;
          }
        })
        this.beforeStr = modifiedText
        data.used = true

        console.log('====================================');
        console.log(modifiedText);
        console.log('====================================');
      }
    },
    mounted() {
      const Diff = require('diff')
      const { nanoid } = require('nanoid');
      const d = Diff.diffChars(this.beforeStr
      , this.affterStr)
      d.forEach(item => {
        item.id = nanoid(),
        item.used = false
      })
      console.log(d)
      this.resultStr = d
    }
  }
</script>
<style>
#app {
  width: 800px;
  padding: 50px;
  margin: auto;
}
.green {
  color: #42b983;
}
.red {
  color: red;
}
.grey {
  color: #808080;
}
</style>
