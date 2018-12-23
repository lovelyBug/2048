<template>
  <div class="wraper">
    <div class="header">
      <h1 class="title">2048</h1>
      <div class="score">
        <div class="text_style">
          <div>SCORE</div>
          <div class="score_num">{{ score }}</div>
        </div>
        <div class="text_style">
          <div>BEST</div>
          <div class="score_num">{{ best_score }}</div>
        </div>
      </div>
    </div>
    <div
      class="btn btn-mg"
      @click="new_game">New game</div>
    <div>
      <div
        class="over"
        v-if="over">
        <p>Game over!</p>
        <div
          class="btn"
          @click="new_game">
          Try again
        </div>
      </div>
      <div class="box">
        <div
          v-for="(row, index) in list"
          :key="index"
          class="row">
          <div
            v-for="(col, index) in row"
            :key="index"
            class="col"
            :class="'n-'+col">
              {{ col }}
            </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      score: 0,
      // 最高分数，本地未保存最高分数时，最高分数显示为0
      best_score: localStorage.getItem('bestscore') ? localStorage.getItem('bestscore') : 0,
      // 游戏是否结束
      over: false,
      // 列表数据源，组件mounted时填充数据
      list: [],
      // 棋盘大小
      size: 4,
      start: {},
      // 2 / 4 出现次数的比率
      probability: 0.9,
      // 格子是否移动
      hasMove: false
    }
  },

  mounted () {
    this.init()
  },

  methods: {
    /**
     *  初始化游戏
     */
    init () {
      this.new_game()
      document.addEventListener('keyup', this.key_down)
      document.querySelector('.box').addEventListener('touchstart', this.touch_start)
      document.querySelector('.box').addEventListener('touchend', this.touch_end)
    },
    /**
     * 初始化数组，开始一局新的游戏
     */
    new_game () {
      this.score = 0
      this.over = false
      this.list = Array.from(Array(this.size)).map(() => Array(this.size).fill(undefined))
      // 开局随机出现两个格子
      this.add_new_cell()
      this.add_new_cell()
    },
    /**
     * 添加一个空格子
     */
    add_new_cell () {
      if (this.has_available_cells()) {
        let [x, y] = this.gat_random_available_space_cell()
        // 出现2 和 4的概率比为9 : 1
        this.list[x][y] = Math.random() < this.probability ? 2 : 4
      }
    },

    /**
     * 获取所有的空格子坐标
     */
    available_space_cells () {
      let spaceCells = []
      for (let i = 0; i < this.size; i++) {
        for (let j = 0; j < this.size; j++) {
          if (!this.list[i][j]) {
            spaceCells.push([i, j])
          }
        }
      }
      return spaceCells
    },
    /**
     * 随机获取一个空格子坐标
     */
    gat_random_available_space_cell () {
      // 获取打乱后的数组
      let cells = this.upset(this.available_space_cells())
      if (cells.length) {
        return cells[this.random_num(cells.length)]
      }
    },
    /**
     * 打乱数组，让新格子出现的更加随机
     */
    upset (arr) {
      let l = arr.length
      let j
      while (l--) {
        j = parseInt(Math.random() * l)
        arr[l] = arr[j]
        arr[j] = arr[l]
      }
      return arr
    },
    /**
     * 获取指定范围内的随机数
     */
    random_num (num) {
      return Math.floor(Math.random() * num)
    },
    /**
     * 根据移动前后的二维数组作对比以判断格子是否有移动
     */
    is_same_list (oldList, newList) {
      for (var i = 0; i < oldList.length; i++) {
        for (var j = 0; j < oldList[i].length; j++) {
          if (oldList[i][j] !== newList[i][j]) {
            return false
          }
        }
      }
      return true
    },
    /**
     * 判断矩阵是否还可以继续合并，移动，对比矩阵内元素周围是否有和自身相等的元素
     */
    can_continue_move () {
      for (let i = 0; i < this.size; i++) {
        for (let j = 0; j < this.size - 1; j++) {
          if (this.list[i][j] === this.list[i][j + 1] || this.list[j][i] === this.list[j + 1][i]) {
            return true
          }
        }
      }
      return false
    },
    /**
     * 手势滑动或者上下左右键触发的事件
     */
    handle_move_event (code) {
      this.hasMove = false
      this.clockwise_rotation(code)
      // 如果有格子移动，随机增加一个新格子
      if (this.hasMove) {
        this.add_new_cell()
      }
      // 保存最高分
      this.save_best_score()
      // 如果没有格子移动,已经没有空格子，并且没有可以合并的格子，说明游戏结束
      if (!this.hasMove && !this.has_available_cells() && !this.can_continue_move()) {
        this.over = true
      }
    },
    /**
     * 监听键盘事件
     */
    key_down (e) {
      let code = -1
      switch (e.keyCode) {
        // 左键
        case 37:
          code = 0
          break
        // 上键
        case 38:
          code = 1
          break
        // 右键
        case 39:
          code = 2
          break
        // 下键
        case 40:
          code = 3
          break
      }
      // 只有按下 上 下 左 右 键时才起作用
      code !== -1 && this.handle_move_event(code)
    },
    /**
     * 滑动开始
     */
    touch_start (e) {
      this.start['x'] = e.changedTouches[0].pageX
      this.start['y'] = e.changedTouches[0].pageY
    },
    /**
     * 滑动结束
     */
    touch_end (e) {
      let curPoint = e.changedTouches[0]
      let x = curPoint.pageX - this.start.x
      let y = curPoint.pageY - this.start.y
      let xx = Math.abs(x)
      let yy = Math.abs(y)
      let code = 0
      // 移动范围太小 不处理
      if (xx < 50 && yy < 50) return
      // 横向滑动
      if (xx >= yy) {
        code = x < 0 ? 0 : 2
      } else {
        // 纵向滑动
        code = y < 0 ? 1 : 3
      }
      this.handle_move_event(code)
    },
    /**
     * 矩阵顺旋转次数
     */
    clockwise_rotation (times) {
      // 矩阵逆时针旋转times次
      let arr = this.unticlockwise_rotation(Array.from(this.list), times).map((item, index) => {
        return this.move_left(item)
      })
      // 旋转回来
      this.list = this.unticlockwise_rotation(arr, this.size - times)
    },
    /**
     * 矩阵逆旋转
     */
    unticlockwise_rotation (arr, n) {
      // 向左边移动时保持数组不变
      if (n % 4 === 0) {
        return arr
      }
      let tempArr = Array.from(Array(this.size)).map(() => Array(this.size).fill(undefined))
      for (let i = 0; i < this.size; i++) {
        for (let j = 0; j < this.size; j++) {
          tempArr[this.size - 1 - i][j] = arr[j][i]
        }
      }
      if (n > 1) {
        tempArr = this.unticlockwise_rotation(tempArr, n - 1)
      }
      return tempArr
    },
    /**
     * 矩阵左移
     */
    move_left (list) {
      // 非空格子列表
      let nonEmptyLists = []
      for (let i = 0; i < this.size; i++) {
        if (list[i]) {
          nonEmptyLists.push({
            x: i,
            merged: false,
            value: list[i]
          })
        }
      }
      nonEmptyLists.forEach(item => {
        let farthest = this.farthest_left_position(list, item)
        let next = list[farthest - 1]
        if (next && next === item.value && !nonEmptyLists[farthest - 1].merged) {
          list[farthest - 1] = next * 2
          list[item.x] = undefined
          item = {
            x: farthest - 1,
            merged: true,
            value: next * 2
          }
          this.score += next * 2
          // 如果有合并，说明格子有移动
          if (!this.hasMove) {
            this.hasMove = true
          }
        } else if (farthest !== item.x) {
          list[farthest] = item.value
          list[item.x] = undefined
          item.x = farthest
          // 交换位置，说明格子有移动
          if (!this.hasMove) {
            this.hasMove = true
          }
        }
      })
      return list
    },
    /**
     * 每行最左边格子的x坐标
     */
    farthest_left_position (list, cell) {
      let farthest = cell.x
      while (farthest > 0 && !list[farthest - 1]) {
        farthest -= 1
      }
      return farthest
    },
    /**
     * 是否还有空格存在
     */
    has_available_cells () {
      // !!表示数据类型限制为bool
      return !!this.available_space_cells().length
    },
    /**
     * 保存最高分数
     */
    save_best_score () {
      let bestScore = localStorage.getItem('bestscore')
      if (bestScore) {
        if (this.score > bestScore) {
          localStorage.setItem('bestscore', this.score)
          this.best_score = this.score
        }
      } else {
        localStorage.setItem('bestscore', this.score)
        this.best_score = this.score
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@keyframes x0{to{left:0;}}
.x0{animation:x0 .2s ease  forwards}
@keyframes x25{to{left:25%;}}
.x25{animation:x25 .2s ease  forwards}
@keyframes x50{to{left:50%;}}
.x50{animation:x50 .2s ease  forwards}
@keyframes x75{to{left:75%;}}
.x75{animation:x75 .2s ease  forwards}

@keyframes y0{to{top:0;}}
.y0{animation:y0 .2s ease  forwards}
@keyframes y25{to{top:25%;}}
.y25{animation:y25 .2s ease  forwards}
@keyframes y50{to{top:50%;}}
.y50{animation:y50 .2s ease  forwards}
@keyframes y75{to{top:75%;}}
.y75{animation:y75 .2s ease  forwards}

.wraper {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background-color: #faf8ef;
  width: 100%;
  height: 100%;
  .header {
    width: 400px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #776e65;
    .title {
      font-size: 60px;
    }
    .score {
      display: flex;
      justify-content: space-between;
      height: 80px;
      div {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding-left: 5px;
        padding-right: 5px;
        border-radius: 5px;
        background: #bbada0;
        .score_num {
          font-size: 25px;
          font-weight: bold;
          color: #ffffff;
        }
        &:last-child {
          margin-left: 20px;
        }
      }
    }
  }

  .over {
    position: absolute;
    width: 400px;
    height: 400px;
    background: rgba(238, 228, 218, 0.73);
    z-index: 1000;
    border-radius: 5px;
    text-align: center;
    color: #8f7a66;
    p {
      font-size: 60px;
      font-weight: bold;
      height: 60px;
      line-height: 60px;
    }
  }

  .btn {
    display: inline-block;
    padding: 0 20px;
    height: 40px;
    line-height: 40px;
    border-radius: 5px;
    cursor: pointer;
    text-align: center;
    color: #f9f6f2;
    background: #8f7a66;
    &.btn-mg {
      margin-bottom: 10px;
    }
  }

  .box {
    width: 400px;
    height: 400px;
    padding: 15px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    box-sizing: border-box;
    border-radius: 5px;
    background: #bbada0;
    .row {
      width: 100%;
      height: 23%;
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      .col {
        width: 23%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 24px;
        border-radius: 3px;
        background: #cec1b3;
        &.n-2 {
          background: #f8f3e8;
        }
        &.n-4 {
          background: #ede0c8;
        }
        &.n-8 {
          background: #f26179;
        }
        &.n-16 {
          background: #f59563;
        }
        &.n-32 {
          background: #f67c5f;
        }
        &.n-64 {
          background: #f65e36;
        }
        &.n-128 {
          background: #edcf72;
        }
        &.n-256 {
          background: #edcc61;
        }
        &.n-512 {
          background: #9c0;
        }
        &.n-1024 {
          background: #3365a5;
        }
        &.n-2048 {
          background: #09c;
        }
        &.n-4096 {
          background: #a6bc;
        }
        &.n-8192 {
          background: #93c;
        }
      }
    }
  }
}
</style>
