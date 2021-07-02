<template>
  <div class="home">
    <el-row :gutter="20" type="flex" justify="center">
      <el-col :span="8">
        <el-form
          :model="form"
          ref="form"
          :rules="rules"
          label-width="100px"
          label-position="left"
          :inline="false"
          size="normal"
        >
          <!-- 第一项 -->
          <el-form-item label="题目数量">
            <!-- select选择器 -->
            <el-select v-model="form.count" placeholder="选择数量" clearable>
              <el-option
                v-for="item in countOptions"
                :key="item"
                :label="item"
                :value="item"
              >
              </el-option>
            </el-select>
          </el-form-item>
          <!-- 第二项 -->
          <el-form-item label="计算类型">
            <el-checkbox-group v-model="form.operators">
              <el-checkbox
                v-for="item in operateType"
                :key="item.id"
                :label="item"
              >
                {{ item.label }}
              </el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <!-- 第三项 -->
          <!-- <el-form-item label="计算范围">
            <el-slider v-model="form.range" :min="100" :step="100" :max="1000">
            </el-slider>
          </el-form-item> -->
          <el-form-item>
            <el-button type="primary" @click="onSubmit">立即创建</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      form: {
        count: 10,
        operators: [],
        // 考虑最小范围等于最大范围的情况
        range: 100,
      },
      rules: {},
      // 题目数量选项
      countOptions: [10, 20, 40, 50],
      // 题目类型
      operateType: [
        { id: 1, label: "加法", value: "+", show: "＋" },
        { id: 2, label: "减法", value: "-", show: "－" },
        { id: 3, label: "乘法", value: "*", show: "×" },
        { id: 4, label: "除法", value: "/", show: "÷" },
      ],
    };
  },
  methods: {
    onSubmit() {
      console.log(this.form);
      // return;
      // 跳转页面
      this.$router.push({
        // name: "Calcu",
        // params: {
        //   count: this.form.count,
        //   operators: this.form.operators,
        //   range: this.form.range,
        // },
        path: "/calcu",
        query: {
          count: this.form.count,
          operators: this.form.operators,
          range: this.form.range,
        },
      });
    },
  },
  components: {},
};
</script>
<style scoped>
</style>