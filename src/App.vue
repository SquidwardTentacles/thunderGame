<template>
  <div id="app">

    <div class="thunder">
      <div v-for="(item, indexO) in 10"
           :key="indexO"
           class="flexbox">

        <!-- :class="{'isthunder':isthunderFunc(index)}" -->
        <div class="innerbox"
             :data-id='returnId(indexO,index)'
             v-for="(item, index) in 10"
             :key="index">
          {{returnId(indexO,index)}}
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  mounted () {
    this.onloadFunc()
  },
  data () {
    return {
      thunderTotalNum: 0,
      thunerArr: [],
      num2: 0,
    }
  },
  watch: {
    num2 () {
      this.thunderTotalNum++
    }
  },
  computed: {
    // isthunderFunc () {
    //   return (index) => {
    //     for (let i = 0; i < this.thunerArr.length; i++) {
    //       if (this.thunerArr[i] === index) {
    //         return true
    //       }
    //     }
    //     return false
    //   }
    // },
    returnId () {
      // let fa = String(fi),
      //   sei = String(si)
      return (fi, si) => {
        return String(fi) + String(si)
      }
    }
  },
  methods: {
    onloadFunc () {
      // let arr = []
      // 用随机数生成10个随机的地雷id
      // for (let i = 0; i < 10; i++) {
      //   let id = Math.floor(Math.random() * 100)
      //   arr.push(id)
      // }
      // this.thunerArr = arr
      // 地雷数
      this.thunerArr = this.createThunder()
      // 生成一个默认的雷盘
      let defThunder = []
      let row = 10 //行
      let col = 10 //列
      for (let r = 0; r < row; r++) {
        let arr = []
        defThunder.push(arr)
        for (let c = 0; c < col; c++) {
          defThunder[r].push(0)
        }
      }
      console.log(defThunder);
      // 将地雷填充到雷盘
      for (let i = 0; i < this.thunerArr.length; i++) {
        let thunderId = String(this.thunerArr[i])
        let tx = 0, ty = ''
        if (thunderId.length > 1) {
          tx = parseInt(thunderId.slice(0, 1))
          ty = parseInt(thunderId.slice(1, 2))
        } else {
          ty = parseInt(thunderId.slice(0, 1))
        }
        defThunder[tx][ty] = -1
      }
      console.log(defThunder);
      // 查找周围雷的个数
      for (let r = 1; r < row - 1; r++) {
        for (let c = 1; c < col - 1; c++) {
          if (col[c] === -1) {
            // 如果当前是雷 则周围9个框数字加1
            // 上一行数字操作
            let id = 3
            // 下标 从右到左 因为一开始就执行自减 所以要多加1
            let thSubScript = c + 2
            for (let i = 0; i < id; i++) {
              // 上一行 如果当前项为地雷 则不进行操作
              if (defThunder[r - 1][thSubScript--] !== -1) {
                defThunder[r - 1][thSubScript--]++
              }
            }
          }
        }
      }
      console.log(defThunder);

    },
    // 生成炸弹
    createThunder () {
      // 声明一个空数组保存数字 再将数组打乱 取出前10个数字
      let dataArr = []
      let totalNum = 100
      for (let i = 0; i < totalNum; i++) {
        dataArr.push(i)
      }
      // 打乱
      for (let i = 0; i < dataArr.length; i++) {
        // 生成随机数下标 
        let subScriptO = Math.floor(Math.random() * totalNum)
        let subScriptT = Math.floor(Math.random() * totalNum)
        if (subScriptO !== subScriptT) {
          let id = dataArr[subScriptO]
          dataArr[subScriptO] = dataArr[subScriptT]
          dataArr[subScriptT] = id
        }
      }
      // 选取前10作为地雷
      for (let i = 0; i < 10; i++) {
        this.thunerArr.push(dataArr[i])
      }
      return this.thunerArr
    }
  }
}
</script>
<style lang="less">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
html,
body,
#app {
  width: 100%;
  height: 100%;
  margin: 0;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
.flexbox {
  display: flex;
  justify-content: space-around;
  align-items: center;
}
.flexbox.between {
  justify-content: space-between;
}
.flexbox.a-start {
  align-items: flex-start;
}
.flexbox.j-end {
  justify-content: flex-end;
}
.flexbox.j-start {
  justify-content: flex-start;
}
.flexbox.j-center {
  justify-content: center;
}
.flexbox.a-start {
  align-items: start;
}
.thunder {
  width: 500px;
  height: 500px;
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  .innerbox {
    width: 50px;
    height: 50px;
    background-color: #ccc;
    border: 1px solid #007acc;
    box-sizing: border-box;
    &.isthunder {
      background-color: #000;
    }
  }
}
</style>
