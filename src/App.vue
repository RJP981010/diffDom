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
    <span class="grey" v-html="beforeStr"></span>
    <hr/>
    <p>他们的</p>
    <span class="grey" v-html="affterStr"></span>
    <hr/>
    <p>冲突</p>
    <template v-for="(item,index) in resultStr">
      <el-popconfirm
        v-if="item.added || item.removed"
        @confirm="confirm(item)"
        :key="index + 'a'"
        title="确定应用这一段内容吗？"
      >
        <span
          v-html="item.value"
          style="cursor: pointer"
          slot="reference"
          :class="item.added ? 'green' :item.removed ? 'red' : 'grey'">
        </span>
      </el-popconfirm>
      <span
        v-else
        v-html="item.value"
        :key="index + 'a'"
        :class="item.added ? 'green' :item.removed ? 'red' : 'grey'">
      </span>
      
    </template>
    <div id="oldHtml" v-html="str1">
    </div>
    <hr/>
    <div id="newHtml" v-html="str2">
    </div>
  <hr/>
    <div id="htmlDiff" v-html="str3">
    </div>
    <!-- <div ref="diffResult" v-html="result"></div> -->
  </div>
</template>
<script>
// import stripTags from 'strip-tags';
import HtmlDiff from 'htmldiff-js';
const striptags = require('striptags');
  export default {
    data() {
      return {
        resultStr: [],
        result: '',
        str1: '<h3>1.读课文</h3>\n<h3>2.小猴子看见的这些东西分别是什么样子的，请用波浪线划出来。</h3>\n<h3>3.汇报，PPT出示．齐读短语</h3>\n<h3>4.说发现</h3>\n<h3>５.小结</h3>\n<h3>用上又&hellip;&hellip;又&hellip;&hellip;可以把事物描绘得更具体、更生动。接下来，我们跟着小猴子的脚步，一起下山看看吧！</h3>\n<table style="border-collapse: collapse; width: 100%; height: 45px;" border="1">\n<tbody>\n<tr style="height: 22.5px;">\n<td style="width: 33.0024%; height: 22.5px;">123</td>\n<td style="width: 33.0024%; height: 22.5px;">456</td>\n<td style="width: 33.0032%; height: 22.5px;">&nbsp;</td>\n</tr>\n<tr style="height: 22.5px;">\n<td style="width: 33.0024%; height: 22.5px;">&nbsp;</td>\n<td style="width: 33.0024%; height: 22.5px;">&nbsp;</td>\n<td style="width: 33.0032%; height: 22.5px;">&nbsp;</td>\n</tr>\n</tbody>\n</table>',
        str2: '<table style="border-collapse: collapse; width: 100%;" border="1">\n<tbody>\n<tr>\n<td style="width: 24.6776%;">&nbsp;</td>\n<td style="width: 24.6776%;">&nbsp;</td>\n<td style="width: 24.6776%;">&nbsp;</td>\n<td style="width: 24.6776%;">&nbsp;</td>\n</tr>\n</tbody>\n</table>\n<h3>1.自由朗读课文</h3>\n<h3>2.看见的这些东西分别是什么样子的，请用波浪线划出来。</h3>\n<h3>3.汇报，PPT出示．齐读短语</h3>\n<h3>4.说发现</h3>\n<p>&nbsp;</p>\n<p><img src="http://lcellimgs.oss-cn-beijing.aliyuncs.com/beikeimage/2023/0413/e5306543a6bc40dc9105acb68078ddad.png" alt="" width="100" height="100" /></p>\n<h3>５.小结</h3>\n<h3>用上又&hellip;&hellip;又&hellip;&hellip;可以把事物描绘得更具体、更生动。接下来，我们跟着小猴子的脚步，一起下山看看吧！</h3>\n<table style="border-collapse: collapse; width: 100%; height: 45px;" border="1">\n<tbody>\n<tr style="height: 22.5px;">\n<td style="width: 33.0024%; height: 22.5px;">123</td>\n<td style="width: 33.0024%; height: 22.5px;">456</td>\n<td style="width: 33.0032%; height: 22.5px;">123123123123</td>\n</tr>\n<tr style="height: 22.5px;">\n<td style="width: 33.0024%; height: 22.5px;">as2q23r</td>\n<td style="width: 33.0024%; height: 22.5px;">123123</td>\n<td style="width: 33.0032%; height: 22.5px;">1dfsdf</td>\n</tr>\n</tbody>\n</table>',
        str3: '',
        beforeStr: '《13.3等腰三角形》主要是在探索了三角形全等及轴对称性质的基础上进行的，进一步认识特殊的轴对称图形──等腰三角形，主要探索等腰三角形“等边对等角”和“等腰三角形的三线合一”两个性质。本节内容既是前面知识的深化和应用，又是今后学习等边三角形的预备知识，还是证明角相等、线段相等及两直线互相垂直的重要依据，具有承上启下的重要作用',
        affterStr: '123《13.3等腰三角形》主要是在探索了三角形全等及轴对称性质的基础上进行的，进一步认识特殊的轴对称图形──等腰三角形，主要探索等腰三角形“等边对等角”和“等腰三角形的三线合一”两个性质。本节内容既是前面知识的深化和应用，又是今后学习等边三角形的预备知识，明角相等、线段相等及两直线互相垂直的重要依据，具有承上启下的重要作用'
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
      },
      handleHtml(html) {
        const text = striptags(html);
        console.log(text);
      }
    },
    mounted() {
      const Diff = require('diff')
      const { nanoid } = require('nanoid');
      // const stripTags = require('strip-tags');
      // this.beforeStr = striptags(this.beforeStr)
      // this.affterStr = striptags(this.affterStr)
      this.handleHtml('<div><p>Hello <strong>world</strong>!</p></div>')
      // console.log(stripTags())
      const d = Diff.diffChars(this.beforeStr, this.affterStr)
      d.forEach(item => {
        item.id = nanoid()
        item.used = false
        const color = item.added ? 'green' : item.removed ? 'red' : 'black';
        const value = item.value.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
        this.result += `<span style="color:${color}">${value}</span>`;
      })
      // let oldHtml = document.getElementById('oldHtml');
      // let newHtml = document.getElementById('newHtml');
      // let diffHtml = document.getElementById('diffHtml');
      
      this.str3 = HtmlDiff.execute(this.str1, this.str2);
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
.diffins {
  background-color: lightgreen;
}

.diffdel {
  background-color: lightcoral;
}

.diffmod {
  background-color: lightskyblue;
}
</style>
