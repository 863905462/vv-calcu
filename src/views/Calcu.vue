<template>
  <div>
    <el-row :gutter="20" type="flex" justify="center">
      <el-col :span="8">
        <el-progress
          :percentage="progress"
          :stroke-width="20"
          :color="progressColor"
        ></el-progress>

        <!-- 折叠面板 -->
        <el-collapse v-model="activeName" accordion>
          <el-collapse-item
            :title="setTitle(index)"
            :name="index + 1 + ''"
            v-for="(item, index) in questions"
            :key="index"
          >
            <!-- 试题展示 -->
            <span class="questionSpan">{{ item }}</span>
            <!-- 答案展示 -->
            <el-input
              class="inputs"
              size="normal"
              v-model="answer"
              clearable
              @change="next"
              @blur="setFocus(index)"
            ></el-input>
          </el-collapse-item>
        </el-collapse>

        <el-button type="primary" size="default" @click="onSubmit"
          >提交</el-button
        >
      </el-col>
      <el-col :span="4" :offset="0">
        <el-tag
          class="tiemr"
          type="primary"
          size="normal"
          effect="dark"
          v-text="timerFormat(timer)"
        ></el-tag>
      </el-col>
    </el-row>

    <el-dialog
      :title="dialogTitle"
      :visible.sync="scoreVisible"
      width="30%"
      :close-on-click-modal="false"
      @close="dialogClosed"
    >
      <el-progress
        v-if="progressVisible"
        type="circle"
        :percentage="dialogPercent"
      ></el-progress>
      <div v-else>
        <span class="scoreSpan">得分:</span>
        <span class="scoreSpan" v-text="score"></span>
        <div>
          <span class="timerSpan">耗时:</span>
          <span class="timerSpan" v-text="timerFormat(timer)"></span>
        </div>
      </div>

      <span slot="footer">
        <!-- <el-button @click="scoreVisible = false">Cancel</el-button> -->
        <el-button type="primary" @click="backToHome">返回首页</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "Calcu",
  data() {
    return {
      form: {},
      rules: {},
      // 进度
      progress: 0,
      // 进度条颜色
      progressColor: ["#0080ff", "#03a6ff"],
      // 题目折叠项的激活
      activeName: "1",
      // 题目信息
      count: 0,
      operators: [],
      range: 0,
      // 问题
      questions: [],
      // 回答数组
      answers: [],
      // 答案数组
      solutions: [],
      // 临时回答
      answer: null,
      // 对话框
      scoreVisible: false,
      // 对话框title
      dialogTitle: "AI评估",
      // 对话框评分进度
      dialogPercent: 0,
      // 对话框评分显示
      progressVisible: true,
      // 分数
      score: 0,
      // 计时器数值
      timer: 0,
      // 计时器对象
      timerObj: {},
    };
  },
  // props: ["count", "operators", "range"],
  methods: {
    next(event) {
      // 字符串转换int 并加1
      let index = parseInt(this.activeName);
      let activeIndex = index + 1;
      console.log("activeIndex " + activeIndex);
      // int类型转换字符串 并赋值
      this.activeName = activeIndex + "";

      // 因为标识第n题，所以储存是-1
      // 储存回答
      this.answers[index - 1] = this.answer;
      // 临时回答置空
      this.answer = null;

      console.log(this.answers);

      // 答案数
      let answerCount = 0;
      this.answers.forEach((item) => {
        if (item != null) {
          answerCount += 1;
        }
      });
      // 进度条
      this.progress = (answerCount / this.count) * 100;
      // this.$refs.inputRef.$el.focus();
      console.log();

      // 获取下一个input的焦点
      console.log(typeof event);
    },
    // 设置标题
    setTitle(index) {
      if (this.answers[index] != null) {
        return "第" + (index + 1) + "题：完成";
      } else {
        return "第" + (index + 1) + "题";
      }
    },
    onSubmit() {
      // 计时器停止
      window.clearInterval(this.timerObj);

      // 对话框显示
      this.scoreVisible = true;

      // 分数统计
      this.solutions.forEach((item, index) => {
        if (this.answers[index] == item) {
          console.log("第" + (index + 1) + "题，正确");
          this.score += 100 / this.count;
        }
      });

      // 查看耗时
      if (true) {
        // 加减法
        // 标准时间
        let allTime = this.count * 5;
        if (this.timer >= allTime) {
          let minusScore = (this.timer - allTime) * 1;
          let lastScore = this.score - minusScore;
          // 加上时间扣分，如果最后得分小于0，那么等于0
          if (lastScore < 0) {
            this.score = 0;
          } else {
            this.score = lastScore;
          }
        }
      }
      console.log(this.score);

      let myIn = window.setInterval(() => {
        this.dialogPercent += 1;
        if (this.dialogPercent == 100) {
          window.clearInterval(myIn);
          setTimeout(() => {
            this.progressVisible = false;
          }, 1000);
        }
      }, 25);
    },
    dialogClosed() {
      // 进度重置
      this.dialogPercent = 0;
    },
    timerFormat(val) {
      // console.log(val) ;

      let m = Math.floor(val / 60);
      m.toString().padStart(2, "0");
      let s = val % 60;
      s.toString().padStart(2, "0");
      return `${m}分${s}秒`;
    },
    setFocus(index) {
      let inputs = document.querySelectorAll(".inputs");
      let newIndex = parseInt(index + 1);
      inputs[newIndex].querySelector("input").focus();
    },
    backToHome() {
      this.$router.push("/");
    },
  },
  components: {},
  created() {
    console.log(this.$route);
    this.count = this.$route.query.count;
    this.operators = this.$route.query.operators;
    this.range = this.$route.query.range;

    console.log(this.count, this.operators, this.range);
  },
  mounted() {
    // 算题生成

    // 最大范围除以二，因为两个数参与计算
    let max = parseInt(this.range) / 2;

    // 生成数据
    for (var i = 0; i < this.count; i++) {
      // 生成一道题
      // 操作符
      let operatorIndex = Math.floor(Math.random() * this.operators.length);
      // 操作符对象，包括计算操作符、展示操作符、id、label
      let operatorObj = this.operators[operatorIndex];

      // 特殊情况3
      // 如果是除法计算，计算范围再一步缩小到33以内，保证结果在1000以内
      if (operatorObj.value == "/") {
        max = 33;
      }

      // 数字一，小于该范围
      let n1 = Math.floor(Math.random() * max);

      let n2 = Math.floor(Math.random() * max);

      // 特殊情况1
      // 减法算式中，如果n2>n1需要交换两个数位置，确保结果在正数范围
      if (operatorObj.value == "-" && n2 > n1) {
        n1 = [n2, (n2 = n1)][0];
      }
      // 除法计算，将算式变为乘法，并最终交换被除数与商
      if (operatorObj.value == "/") {
        let sign = this.operators[2];
        let res = eval(`${n1}*${n2}`);

        n1 = [res, (res = n1)][0];

        console.log(`第一个数${n1}   ${n2}=${res}`);
        // this.solutions.push(res);
        // this.questions.push(`${n1}${operatorObj.show}${n2}=`);
        // continue;
      }
      let res = eval(`${n1}${operatorObj.value}${n2}`);

      // 特殊情况2

      this.solutions.push(res);
      this.questions.push(`${n1}${operatorObj.show}${n2}=`);
    }
    // 定时器

    this.timerObj = window.setInterval(() => {
      this.timer += 1;
    }, 1000);
  },
};
</script>
<style scoped>
@font-face {
  font-family: electronicFont;
  src: url(../assets/font/DS-DIGIT.TTF);
}
.tiemr {
  font-size: 24px;
  line-height: 40px;
  width: 120px;
  height: 40px;
}

.questionSpan {
  font-size: 28px;
}

.scoreSpan {
  font-family: "electronicFont";
  font-size: 60px;
  margin: 10px;

  /* 渐变字体 */
  background-image: -webkit-linear-gradient(
    bottom,
    rgb(30, 144, 255),
    rgb(0, 255, 127)
  );
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.timerSpan {
  font-family: "electronicFont";
  font-size: 20px;
  margin: 10px;

  /* 渐变字体 */
  background-image: -webkit-linear-gradient(
    bottom,
    rgb(255, 78, 80),
    rgb(249, 212, 35)
  );
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
</style>