/**
 * @param {string} fileUrl 文件地址
 */
<template>
  <div ref="parent">
  </div>
</template>
<script>
export default {
  name: 'pdf',
  props: ['fileUrl'],
  watch: {
    fileUrl (val) {
      if (this.$refs.parent) {
        this.$refs.parent.innerHTML = ''
      }
      if (val) {
        this.getPdfFile(val)
      }
    }
  },
  data () {
    return {
      xhr: new XMLHttpRequest()
    }
  },
  methods: {
    getPdfFile (url) {
      const _this = this
      this.xhr.abort() // 请求中止
      this.xhr.open('get', url, true)
      this.xhr.responseType = 'arraybuffer'
      this.xhr.onload = function () {
        if (this.status === 200) {
          let typedarray = new Uint8Array(this.response)
          _this.renderFile(typedarray)
          _this.$emit('onLoad', typedarray)
        }
      }
      this.xhr.send()
    },
    renderFile (typedarray) {
      // eslint-disable-next-line
      let _PDFJS = PDFJS
      // 加载字体文件，避免字体显示不完成，——不加这两行不显示日期
      _PDFJS.cMapUrl = 'https://cdn.jsdelivr.net/npm/pdfjs-dist@2.0.288/cmaps/'
      _PDFJS.cMapPacked = true
      _PDFJS.getDocument(typedarray).then(pdf => {
        if (pdf) {
          let pageNum = pdf.numPages
          for (let i = 1; i <= pageNum; i++) {
            let canvas = document.createElement('canvas')
            if (this.$refs.parent) {
              this.$refs.parent.appendChild(canvas)
            }
            let context = canvas.getContext('2d')
            this.openPage(pdf, i, context)
          }
        }
      })
    },
    openPage (pdfFile, pageNumber, context) {
      let scale = 8
      pdfFile.getPage(pageNumber).then(page => {
        let viewport = page.getViewport(scale)
        let canvas = context.canvas
        canvas.width = viewport.width
        canvas.height = viewport.height
        canvas.style.width = '100%'
        canvas.style.height = '100%'
        let renderContext = {
          canvasContext: context,
          viewport: viewport
        }
        page.render(renderContext).then(() => {
          // 渲染完成
          this.$emit('onRender')
        })
      })
    }
  }
}
</script>

<style>
</style>
