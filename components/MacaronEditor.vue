<template>
  <v-container>
    <v-row align="center">
      <v-col justfy="center" align="center">
        <v-btn
          class="margin-button"
          width="200"
          color="primary"
          outlined
          rounded
          block
          @click="$refs.pickcolor.click()"
          >เลือกสีมาการอง</v-btn
        >
        <input
          ref="pickcolor"
          v-model="color"
          type="color"
          value="#ffffff"
          hidden
        />
        <!-- <v-text-field
          lable="input Text"
          v-model="textConfig.text"
        ></v-text-field>
        <v-btn v-on:click="clearText()">Clear Text</v-btn> -->
        <v-btn
          class="margin-button"
          width="200"
          color="primary"
          outlined
          rounded
          block
          @click="$refs.uploadimg.click()"
          >อัพโหลดรูปภาพ</v-btn
        >
        <input
          ref="uploadimg"
          type="file"
          accept="image/*"
          hidden
          @change="handleUploadImage"
        />
        <v-btn
          class="margin-button"
          width="200"
          color="red"
          outlined
          rounded
          block
          @click="clearImage()"
          >ลบรูปภาพ</v-btn
        >
      </v-col>
      <v-col justfy="center" align="center">
        <v-stage
          ref="stage"
          :config="stageSize"
          @mousedown="handleStageMouseDown"
          @touchstart="handleStageMouseDown"
        >
          <v-layer ref="layer">
            <v-image
              :config="{
                image: imageMacaron,
                fill: color,
              }"
            />
          </v-layer>
          <v-layer :config="layerConfig">
            <v-image
              :config="{
                image: image,
                draggable: true,
                name: 'myImg',
              }"
            />
            <v-text :config="textConfig" />
            <v-transformer ref="transformer" />
          </v-layer>
        </v-stage>
      </v-col>
    </v-row>

    <v-footer class="pa-4" color="transparent" fixed>
      <v-btn color="primary" depressed rounded block dark @click="saveImg()"
        >ถัดไป</v-btn
      >
    </v-footer>
  </v-container>
</template>

<script>
const width = 300
const height = 300

export default {
  data() {
    return {
      file: null,
      color: '#ffffff',
      canvas: null,
      stageSize: {
        width,
        height,
      },
      layerConfig: {
        clipFunc: (ctx) => {
          ctx.arc(150, 150, 145, 0, Math.PI * 2, false)
        },
      },
      textConfig: {
        text: '',
        x: 150,
        y: 150,
        draggable: true,
        fill: 'black',
        name: 'myText',
      },
      circleConfig: {
        x: width / 2,
        y: height / 2,
        radius: 145,
        fill: 'red',
        stroke: 'black',
        strokeWidth: 4,
      },
      selectedShapeName: '',
      imageMacaron: null,
      image: null,
    }
  },
  created() {
    this.setImageMacaron()
    this.setImgageInit('')
  },
  methods: {
    handleUploadImage(event) {
      const file = event.target.files[0]

      this.setImgage(window.URL.createObjectURL(file))
    },
    clearImage() {
      this.image = null
    },
    clearText() {
      this.textConfig.text = ''
    },
    saveImg() {
      const canvas = this.$refs.stage._konvaNode
      // console.log('saveImg', canvas)
      const dataURL = canvas.toDataURL({ pixelRatio: 3 })
      this.downloadURI(dataURL, 'stage.png')
    },

    downloadURI(uri, name) {
      const link = document.createElement('a')
      link.download = name
      link.href = uri
      document.body.appendChild(link)
      link.click()
      document.body.removeChild(link)
    },
    setImageMacaron() {
      // eslint-disable-next-line nuxt/no-globals-in-created
      const image = new window.Image()
      image.src = require('../assets/img/macaron.png')
      image.width = 300
      image.height = 300
      image.onload = () => {
        // set image only when it is loaded
        this.imageMacaron = image
      }
    },
    setImgageInit(imageUrl) {
      const image = new window.Image()
      image.crossOrigin = 'Anonymous'
      image.src = imageUrl
      image.onload = () => {
        image.width = 200
        image.height = 200
        // set image only when it is loaded
        this.image = image
      }
    },
    setImgage(imageUrl) {
      const image = new window.Image()
      image.crossOrigin = 'Anonymous'
      image.src = imageUrl
      image.onload = () => {
        image.width = 200
        image.height = 200
        // set image only when it is loaded
        this.image = image
      }
    },
    handleStageMouseDown(e) {
      // clicked on stage - clear selection
      if (e.target === e.target.getStage()) {
        this.selectedShapeName = ''
        this.updateTransformer()
        return
      }

      // clicked on transformer - do nothing
      const clickedOnTransformer =
        e.target.getParent().className === 'Transformer'
      if (clickedOnTransformer) {
        return
      }

      // find clicked rect by its name
      const name = e.target.name()
      this.selectedShapeName = name
      this.updateTransformer()
    },
    updateTransformer() {
      // here we need to manually attach or detach Transformer node
      const transformerNode = this.$refs.transformer.getNode()
      const stage = transformerNode.getStage()
      const { selectedShapeName } = this

      const selectedNode = stage.findOne('.' + selectedShapeName)
      // do nothing if selected node is already attached
      if (selectedNode === transformerNode.node()) {
        return
      }

      if (selectedNode) {
        // attach to another node
        transformerNode.nodes([selectedNode])
      } else {
        // remove transformer
        transformerNode.nodes([])
      }
      transformerNode.getLayer().batchDraw()
    },
  },
}
</script>
<style scoped>
.margin-button {
  margin-bottom: 5%;
  margin-top: 5%;
}
</style>
