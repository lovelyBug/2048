<template>
  <div class="wraper">
    <div class="header">
      <h1 class="title">2048</h1>
      <div class="score">
        <div>
          <div>SCORE</div>
          <div class="score_num">{{ score }}</div>
        </div>
        <div>
          <div>BEST</div>
          <div class="score_num">{{ best_score }}</div>
        </div>
      </div>
    </div>
    <div class="btn btn-mg">新游戏</div>
    <div>
      <div
        class="over"
        v-if="over">
        <p>Game over!</p>
        <div class="btn">Try again</div>
      </div>
      <div class="box">
        <div
          v-for="(row, index) in list"
          :key="index"
          class="row">
          <div
            v-for="(col, index) in row"
            :key="index"
            class="col">
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
      best_score: 0,
      over: false,
      list: [],
      size: 4
    }
  },

  mounted () {
    this.init()
  },

  methods: {
    // 初始化数组，开始一句新的游戏
    init () {
      this.new_game()
    },

    new_game () {
      this.score = 0
      this.over = false
      this.list = Array.from(Array(this.size)).map(() => Array(this.size).fill(undefined))
      console.log(this.list)
    }
  }

}
</script>

<style lang="scss" scoped>
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
