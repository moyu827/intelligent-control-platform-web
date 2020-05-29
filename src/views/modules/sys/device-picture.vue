<template>
  <el-dialog :visible.sync="dialogTableVisible" :width="width">
      <el-carousel :height="height">
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
        dialogTableVisible: false,
        height: '',
        width: ''
      }
    },
    methods: {
      init (id, height, width) {
        this.dialogTableVisible = true
        this.dataList = id
        this.height = height
        this.width = width
      }
    }
  }
</script>
