<template>
  <el-card class="detail-body-left">
    <div>
      <div class="flex flex-aic">
        <el-select v-model="url" placeholder="请选择">
          <el-option
            v-for="item in pdfList"
            :key="item.name"
            :label="item.name"
            :value="item.url"
          />
        </el-select>
        <el-pagination
          :current-page.sync="page"
          :page-size="1"
          layout="prev, pager, next, jumper"
          :total="numPages"
          @current-change="handleCurrentChange"
        />
        <el-button type="mini" @click="rotate += 90">&#x27F3;</el-button>
        <el-button type="mini" @click="rotate -= 90">&#x27F2;</el-button>
        <el-button type="mini" @click="$refs.pdf.print()">打印</el-button>
      </div>

      <div>
        <div v-if="loadedRatio > 0 && loadedRatio < 1">{{ Math.floor(loadedRatio * 100) }}%</div>
        <Pdf
          ref="pdf"
          :src="url"
          :page="page"
          :rotate="rotate"
          @password="password"
          @progress="loadedRatio = $event"
          @error="error"
          @num-pages="numPages = $event"
          @link-clicked="page = $event"
        />
      </div>
    </div>
  </el-card>
</template>

<script>
import Pdf from 'vue-pdf'
export default {
  name: 'PDF',
  components: {
    Pdf,
  },
  data() {
    return {
      // 地址必须是这样 http://localhost:9528/demo.pdf
      pdfList: [
        {
          name: '1-戴何富与阮小军、阮华超机动车交通事故责任纠纷一审民事判决书',
          url: `${window.location.origin}/1-戴何富与阮小军、阮华超机动车交通事故责任纠纷一审民事判决书.pdf`,
        },
        {
          name: 'demo',
          url: `${window.location.origin}/demo.pdf`,
        },
      ],
      url: '',
      loadedRatio: 0,
      page: 1,
      rotate: 0,
      numPages: 0,
    }
  },
  watch: {
    url() {
      this.getNumPages(this.url)
    },
  },
  methods: {
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`)
    },
    getNumPages(url) {
      let loadingTask = Pdf.createLoadingTask(url)
      loadingTask.promise
        .then(pdf => {
          this.numPages = pdf.numPages
        })
        .catch(() => {
          console.log('获取pdf参数失败')
        })
    },
    password: function(updatePassword, reason) {
      updatePassword(prompt('password is "test"'))
    },
    error: function(err) {
      console.log(err)
    },
  },
}
</script>

<style lang="scss" scoped>
.detail-body-left {
  margin: 20px;
}
</style>
