<template>
  <div id="app">

    <p>剩下地雷数：{{hasThunderNum}}</p>
    <div class="thunder">
      <div class="thunder-block">
        <div v-for="(item, indexO) in defThunderArr"
             :key="indexO"
             class="flexbox">
          <!-- :class="{'isthunder':isthunderFunc(index)}" -->
          <div class="innerbox"
               v-for="(item, index) in 10"
               :key="index"
               @click="thunderClick(indexO,index)">
            <div class="thenderIcon"
                 v-if="defThunderArr[indexO][index].thunderid===-1"></div>
            <div v-else>{{defThunderArr[indexO][index].thunderid}}</div>
            <!-- 上层遮罩 -->
            <div class="cover-box"
                 :class="{'flagStyle':defThunderArr[indexO][index].signStyle}"
                 @contextmenu.prevent.stop='rightClick($event,indexO,index)'
                 v-if="defThunderArr[indexO][index].coverShow"></div>
          </div>
        </div>
      </div>
      <div class="game-popup flexbox "
           v-if="gameOver">
        <p>{{coverContent}}</p>
        <p class="re-begion"
           @click="reloadBegion">重新开始</p>
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
      defThunderArr: [],
      coverHiddenArr: [],
      row: 10,
      col: 10,
      click: 0,
      // 剩下地雷数 
      hasThunderNum: 10,
      // 默认展开的盒子数量（第一次点击展开的盒子）
      showboxNum: 0,
      // 地雷个数
      thunderNum: 10,
      // 当左键点击的是雷 就不用显示完成弹窗
      overPopup: true,
      gameOver: false,
      coverContent: '恭喜获胜'
    }
  },
  watch: {
    num2 () {
      this.thunderTotalNum++
    }
  },
  computed: {
    // 控制遮罩层是否显示
    coverShow () {
      return (x, y) => {
        let clickXY = String(x) + String(y)
        if (this.coverHiddenArr.length === 0) return true
        this.coverHiddenArr.map((currentValue) => {
          return currentValue === clickXY
        })
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
          let obj = {
            thunderid: 0,
            // 默认遮罩层是否显示
            coverShow: true,
            // 标记样式是否显示
            signStyle: false
          }
          defThunder[r].push(obj)
        }
      }
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
        defThunder[tx][ty].thunderid = -1
      }
      // 查找周围雷的个数
      for (let tr = 0; tr < row; tr++) {
        for (let tc = 0; tc < col; tc++) {
          // 如果当前项是雷
          if (defThunder[tr][tc].thunderid === -1) {
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
                if (sty >= 0) {
                  if (defThunder[tr - 1][sty].thunderid !== -1) {
                    //  如果是列的第一行 则当sty(横坐标不等于雷的横坐标才进行操作) 对于雷是列的第一个进行的判断
                    if (tc !== 0 || sty !== (tc - 1)) {
                      defThunder[tr - 1][sty].thunderid++
                    }
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
            if (tr !== forRow) {
              for (let i = 0; i < underSubFor; i++) {
                underSubScriper--
                let underId = underSubScriper
                // 如果是雷就不进行操作
                if (defThunder[tr + 1][underId].thunderid !== -1) {
                  defThunder[tr + 1][underId].thunderid++
                }
              }
            }
            // -----------------------
            // 左右 

            if (tc === 0) {
              defThunder[tr][tc + 1].thunderid == -1 ? '' : defThunder[tr][tc + 1].thunderid++
            } else if (tc === (col - 1)) {
              defThunder[tr][tc - 1].thunderid == -1 ? '' : defThunder[tr][tc - 1].thunderid++
            } else {
              defThunder[tr][tc - 1].thunderid == -1 ? '' : defThunder[tr][tc - 1].thunderid++
              defThunder[tr][tc + 1].thunderid == -1 ? '' : defThunder[tr][tc + 1].thunderid++
            }
          }
        }
      }
      this.defThunderArr = defThunder
      console.log(defThunder);

    },
    // 生成炸弹
    createThunder () {
      // 声明一个空数组保存数字 再将数组打乱 取出前10个数字
      let dataArr = []
      this.thunerArr = []
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
    },
    // 单击事件
    thunderClick (x, y) {
      this.defThunderArr[x][y].coverShow = false
      this.otherShow(x, y)
      let thunderNum = 0
      thunderNum = this.showboxNum
      this.defThunderArr.map((currentVal) => {
        currentVal.map((curVal) => {
          if (curVal.coverShow === false && curVal.thunderid !== -1) {
            thunderNum++
          }
        })
      })
      if ((thunderNum + this.thunderNum) === (this.col * this.row)) {
        if (this.overPopup) {
          this.gameOver = true
          this.coverContent = '恭喜获胜'
        }
        this.defThunderArr.map((cur) => {
          cur.map((curi) => {
            curi.coverShow = false
          })
        })
      }
      console.log(thunderNum, this.thunderNum);
    },
    otherShow (x, y) {
      console.log('click');

      // 如果当前项是雷 则显示所有
      if (this.defThunderArr[x][y].thunderid === -1) {
        this.overPopup = false
        this.gameOver = true
        this.coverContent = '你输了'
        this.hasThunderNum = 10
        this.defThunderArr.map((cur) => {
          cur.map((curi) => {
            curi.coverShow = false
          })
        })
      } else {
        if (this.click === 1) return
        // 显示相邻的20个不为雷的格子 声明显示位置的xy坐标 x 4 y 5 展开位置从点击位置左移一位向下展开
        let showX = (x - 1) < 0 ? 0 : x
        showX = (showX + 2) > this.row ? (this.row - 4) : showX
        let showY = (y + 5) > this.col ? this.col - 5 : y
        let isThunder = 0
        for (let x = showX; x < (showX + 4); x++) {
          for (let y = showY; y < (showY + 5); y++) {
            if (this.defThunderArr[x][y].thunderid !== -1) {
              this.defThunderArr[x][y].coverShow = false
            } else {
              isThunder++
            }
          }
        }
        // 保证要有额外的显示
        isThunder = isThunder > 0 ? isThunder : isThunder + 3
        // if (isThunder > 0) {
        // 附加循环的x坐标
        let addForX = 0
        addForX = (showX + 4) >= this.row ? showX - 1 : showX + 4
        // let addForY = 0
        for (let i = 0; i < isThunder; i++) {
          let elAddfor = this.defThunderArr[addForX][showY++]
          if (elAddfor.thunderid !== -1) {
            elAddfor.coverShow = false
          } else {
            i = i - 1
          }
        }
        console.log(this.showboxNum);

        // }
        // 只允许调用一次显示多个的事件
        this.click++
      }
    },
    // 右键标记
    rightClick (e, x, y) {
      e.preventDefault();
      if (this.defThunderArr[x][y].signStyle === false) {
        this.defThunderArr[x][y].signStyle = true
        if (this.hasThunderNum !== 0) {
          this.hasThunderNum--
        }
      } else if (this.defThunderArr[x][y].signStyle === true) {
        this.defThunderArr[x][y].signStyle = false
        if (this.hasThunderNum <= 10) {
          this.hasThunderNum++
        }
      }

    },
    // 重新开始 
    reloadBegion () {
      this.onloadFunc()
      this.click = 0
      this.gameOver = false
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
  transform: translate(-50%, -70%);
  background-color: #fff;
  .game-popup {
    background: rgba(255, 255, 255, 0.5);
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    .re-begion {
      cursor: pointer;
    }
  }
  .innerbox {
    position: relative;
    width: 50px;
    height: 50px;
    line-height: 50px;
    background-color: #ccc;
    border: 1px solid #007acc;
    box-sizing: border-box;
    &.isthunder {
      background-color: #000;
    }
    .thenderIcon {
      width: 100%;
      height: 100%;
      background: url("../src/assets/thunderBuild.svg") center center no-repeat;
      background-size: 10px 10px;
    }
    .cover-box {
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      background-color: #ccc;
      &.flagStyle {
        background: url("./assets/旗子.svg") center center no-repeat #ccc;
        background-size: 10px 10px;
      }
    }
  }
}
</style>
