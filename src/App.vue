<template>
  <div id="app">

    <div class="thunder">
      <div v-for="(item, indexO) in defThunderArr"
           :key="indexO"
           class="flexbox">

        <!-- :class="{'isthunder':isthunderFunc(index)}" -->
        <div class="innerbox"
             :data-id='returnId(indexO,index)'
             v-for="(item, index) in 10"
             :key="index">
          {{defThunderArr[indexO][index]}}
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      thunderTotalNum: 0,
      thunerArr: [],
      num2: 0,
      defThunderArr: []
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
  mounted () {
    this.onloadFunc()
  },
  methods: {
    onloadFunc () {
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
      for (let tr = 0; tr < row; tr++) {
        for (let tc = 0; tc < col; tc++) {
          // 如果当前项是雷
          if (defThunder[tr][tc] === -1) {
            console.log(defThunder[tr][tc]);
            // 如果当前是雷 则周围9个框数字加1
            // 上一行数字操作
            let id = 3
            // 雷上一行的处理逻辑
            if (tr > 0) {
              // 下标 从右到左 因为一开始就执行自减 所以要多加1
              // 下标规则 如果是列的最后一位 就只需要加一即可 对于雷是列的最后一个进行的判断
              let thSubScript = tc === (col - 1) ? tc + 1 : tc + 2
              // debugger
              if (tc === (col - 1)) id = 2
              for (let ti = 0; ti < id; ti++) {
                // 上一行 并且不是第一行
                // 如果当前项为地雷 则不进行操作 注意 -- 运算符是先赋值再减减
                thSubScript--
                // sty上一行的数组下标
                let sty = thSubScript
                if (defThunder[tr - 1][sty] !== -1) {
                  //  如果是列的第一行 则当sty(横坐标不等于雷的横坐标才进行操作) 对于雷是列的第一个进行的判断
                  if (tc !== 0 || sty !== (tc - 1)) {
                    defThunder[tr - 1][sty]++
                  }
                }
              }
            }
            // 雷下一行的处理逻辑
            // 声明下一行的下标 如果行的最后一个是雷 则下一行的下标与雷的下标相同 (默认会减一次 所以多加1)
            let footerCel = col - 1
            let underSubScriper = (tc === footerCel) ? tc + 1 : tc + 2
            // 如果当前雷的位置是行的第一个或者最后一个就只需要循环两次（操作两次数据）即可
            let colid = col - 1
            let underSubFor = tc === 0 ? 2 : 3
            underSubFor = tc === colid ? 2 : underSubFor
            // 如果是最后一行就不进行操作
            let forRow = row - 1
            if (tr === forRow) return
            for (let i = 0; i < underSubFor; i++) {
              underSubScriper--
              let underId = underSubScriper
              // 如果是雷就不进行操作
              if (defThunder[tr + 1][underId] !== -1) {
                defThunder[tr + 1][underId]++
              }
            }
            // -----------------------
            // 左右 
            if (tc === 0) {
              defThunder[tr][tc + 1]++
            } else if (tc === (col.length - 1)) {
              defThunder[tr][tc - 1]++
            } else {
              defThunder[tr][tc - 1]++
              defThunder[tr][tc + 1]++
            }
          }
        }
      }
      console.log(defThunder);
      this.defThunderArr = defThunder
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
    line-height: 50px;
    background-color: #ccc;
    border: 1px solid #007acc;
    box-sizing: border-box;
    &.isthunder {
      background-color: #000;
    }
  }
}
</style>
