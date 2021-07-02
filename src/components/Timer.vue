<template>
  <div>
    <el-tag type="success" size="normal" effect="light"></el-tag>:
    <el-tag type="success" size="normal" effect="light"></el-tag>:
    <el-tag type="success" size="normal" effect="light"></el-tag>:
  </div>
</template>

<script>
export default {
  name: "Calcu",
  data() {
    return {};
  },
  // props: ["count", "operators", "range"],
  methods: {},
  components: {},
  created() {},
  mounted() {
    // 定时器
  },
};
</script>
<style scoped>
@font-face {
  font-family: electronicFont;
  src: url(../assets/font/DS-DIGIT.TTF);
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
</style>