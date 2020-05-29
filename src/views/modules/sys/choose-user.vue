<template>
  <el-dialog :visible.sync="dialogTableVisible" append-to-body>
    <el-carousel height="500px">
      <el-carousel-item v-for="item in dataList" :key="item">
        <img v-bind:src="item" height="100%" width="100%">
      </el-carousel-item>
    </el-carousel>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        dataList: [],
        dialogTableVisible: true
      }
    },
    methods: {
      init (id) {
        this.dialogTableVisible = true
        this.dataList = id
      }
    }
  }
</script>
