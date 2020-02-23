<template>
  <div id='live2d'
    @mouseover="toolShow = true"
    @mouseout="toolShow = false">
      <div v-show='live2dShow' id='live2d-wraper'>
        <div class="dialog-box" v-html="dialogText" v-show='dialogShow'></div>
        <div id="tools-wraper" v-show="toolShow">
          <span 
            v-for="(tool, index) in tools" 
            class="fa fa-lg" 
            :class="tool.name" 
            :key='index' 
            @click="tool.click"></span>
        </div>
        <canvas id="live2d-canvas" :width="size[0]" :height="size[1]"></canvas>
      </div>
      <div class ='toggle' v-show='!live2dShow' @click="openLive2d">
        <span>看板娘</span>
      </div>
  </div>
</template>

<script>
import axios from 'axios'
import './live2d.min.js'
// TODO: (直接复制vue-live2d的配置)
const tips = {
  copy: [{
    selector: 'document',
    text: ['你都复制了些什么呀，转载要记得加上出处哦！']
  }],
  visibilitychange: [{
    selector: 'document',
    text: ['哇，你终于回来了～']
  }],
  click: [{
    selector: '.veditor',
    text: ['要吐槽些什么呢？', '一定要认真填写喵～', '有什么想说的吗？']
  }, {
    selector: '.vsubmit',
    text: ['输入验证码就可以提交评论啦～']
  }],
	mouseover: [{
		selector: '#live2d #live2d-canvas',
		text: ['(ノ≧∇≦)ノ','biubiu / / /']
	}, {
    selector: '#tools-wraper .fa-comment',
    text: ['猜猜我要说些什么？', '欣赏一些有意思的短句？']
  }, {
    selector: '#tools-wraper .fa-user-circle',
    text: ['想看看我的朋友们吗？']
  }, {
    selector: '#tools-wraper .fa-street-view',
    text: ['变装！']
  }, {
    selector: '#tools-wraper .fa-camera-retro',
    text: ['一起拍照留念？']
  }, {
    selector: '#tools-wraper .fa-info-circle',
    text: ['找到最初的小可爱']
  }, {
    selector: '#tools-wraper .fa-times',
    text: ['就要说再见了吗？']
  }]
}
export default {
  props: {
    apiPath: {
      default: 'https://live2d.fghrsh.net/api',
      type: String
    },
    model:{
      default: () =>[4,77],
      type: Array
    },
    size:{
      default: () => [200,200],
      type: Array
    }
  },
  data(){
    return {
      toolShow: false,
      timer: null,
      modelId: 4,
      modelTexturesId: 77,
      dialogShow: true,
      dialogText: 'ddd',
      live2dShow: true,
      tools: [{
        name: 'fa-comment',
        click: this.changeDialog
      }, {
        name: 'fa-user-circle',
        click: this.changeModel
      }, {
        name: 'fa-street-view',
        click: this.changeTextures
      }, {
        name: 'fa-camera-retro',
        click: this.takePhoto
      }, {
        name: 'fa-info-circle',
        click: this.openHomePage
      }, {
        name: 'fa-times',
        click: this.close
      }]
    }
  },
  mounted(){
    this.modelId = this.model[0] //第一个为模型
    this.modelTexturesId = this.model[1] //第二个为模型贴图，即换皮肤
    this.loadModle()
    this.$nextTick(()=>{
      this.loadEvent()
    })
  },
  methods:{
    loadModle(){
      let url = `${this.apiPath}/get/?id=${this.modelId}-${this.modelTexturesId}`
      const callback = console.log(`Live2D 模型 ${this.modelId}-${this.modelTexturesId} 加载完成`)
      window.loadlive2d('live2d-canvas',url,callback)
    },
    changeDialog(){
      const url = 'https://v1.hitokoto.cn'
      axios.get(url).then((res)=>{
        let {hitokoto}=res.data
        this.showMessage(hitokoto)
      })
    },
    changeModel(){
      const url = `${this.apiPath}/switch/?id=${this.modelId}`
      axios.get(url).then((res)=>{
        let {id, message} = res.data.model
        if(id === 5) id = 7 //因为5，6模型不能加载
        this.modelId = id
        this.loadModle()
        this.showMessage(message)
      })
    },
    changeTextures(){
      const url = `${this.apiPath}/rand_textures/?id=${this.modelId}-${this.modelTexturesId}`
      axios.get(url).then(res=>{
        let { id } = res.data.textures
        this.modelTexturesId = id
        this.loadModle()
      })
    },
    takePhoto(){
      this.showMessage('拍照，我最喜欢拍照拉！')
      window.Live2D.captureName = 'photo.png'
      window.Live2D.captureFrame = true
    },
    openHomePage(){
      open(this.openHomePage)
    },
    close(){
      this.live2dShow = false
    },
    loadEvent(){
      for(let event in tips){
        for(let obj of tips[event]){
          let dom = null
          if(obj.selector === 'document'){
            dom = document
          }else if(document.querySelector(obj.selector)){
            dom = document.querySelector(obj.selector)
          }
          if(dom === null) continue
          dom.addEventListener(event,()=>{
            let textArr = obj.text
            let msg = textArr[Math.floor(Math.random()*textArr.length)] //随机加载一个事件触发后的的显示文字
            this.showMessage(msg, 2000)
            })
          }
        }
      },
      // 用于显示对话框的内容，接收显示文字和出现时间，
      // 主要作用添加定时器，用于定时隐藏
      showMessage(msg = '',time=2000){
        if(this.timer){
          clearTimeout(this.timer)
          this.timer = null
        }else{
          this.dialogShow = true
        }
        this.dialogText = msg
        this.timer = setTimeout(()=>{
          this.dialogShow = false
          this.timer = null
        },time)
      },
      openLive2d(){
        this.live2dShow = true
      }
    },


}
</script>

<style scope>
#live2d{
  position: fixed;
  right:0px;
  bottom:0px
}
.live2d-wraper{
  position: relative;
  margin-right:20px;
}
#tools-wraper{
  display: flex;
  flex-direction: column;
  position: absolute;
  right: 10px;
  bottom: 30px
}
#tools-wraper span{
 cursor: pointer;
 padding: 5px 0px;
}
#tools-wraper span:hover{
  color:rgb(243, 87, 134)
}
.dialog-box{
  width: 90%;
  position: absolute;
  top:-20px;
  background: rgba(241, 95, 163, 0.192);
  padding: 10px;
  border-radius: 10px;
  border: 1px solid rgb(207, 29, 89);
  animation: shake 13s infinite;
}
.toggle{
  background: rgb(243, 87, 134);
  color: aliceblue;
  padding: 5px;
  padding-right: 20px; 
  width: 10px;
  font-size: 0.8rem;
  border-radius: 10px 0px 0px 10px;
  position: relative;
  bottom: 40px;
  right: -30px;
  cursor: pointer;
  transition: 0.5s ease-in-out all
}
.toggle:hover{
  right: 0px
}
  @keyframes shake {
    2% {
      transform: translate(0.5px, -1.5px) rotate(-0.5deg);
    }
    4% {
      transform: translate(0.5px, 1.5px) rotate(1.5deg);
    }
    6% {
      transform: translate(1.5px, 1.5px) rotate(1.5deg);
    }
    8% {
      transform: translate(2.5px, 1.5px) rotate(0.5deg);
    }
    10% {
      transform: translate(0.5px, 2.5px) rotate(0.5deg);
    }
    12% {
      transform: translate(1.5px, 1.5px) rotate(0.5deg);
    }
    14% {
      transform: translate(0.5px, 0.5px) rotate(0.5deg);
    }
    16% {
      transform: translate(-1.5px, -0.5px) rotate(1.5deg);
    }
    18% {
      transform: translate(0.5px, 0.5px) rotate(1.5deg);
    }
    20% {
      transform: translate(2.5px, 2.5px) rotate(1.5deg);
    }
    22% {
      transform: translate(0.5px, -1.5px) rotate(1.5deg);
    }
    24% {
      transform: translate(-1.5px, 1.5px) rotate(-0.5deg);
    }
    26% {
      transform: translate(1.5px, 0.5px) rotate(1.5deg);
    }
    28% {
      transform: translate(-0.5px, -0.5px) rotate(-0.5deg);
    }
    30% {
      transform: translate(1.5px, -0.5px) rotate(-0.5deg);
    }
    32% {
      transform: translate(2.5px, -1.5px) rotate(1.5deg);
    }
    34% {
      transform: translate(2.5px, 2.5px) rotate(-0.5deg);
    }
    36% {
      transform: translate(0.5px, -1.5px) rotate(0.5deg);
    }
    38% {
      transform: translate(2.5px, -0.5px) rotate(-0.5deg);
    }
    40% {
      transform: translate(-0.5px, 2.5px) rotate(0.5deg);
    }
    42% {
      transform: translate(-1.5px, 2.5px) rotate(0.5deg);
    }
    44% {
      transform: translate(-1.5px, 1.5px) rotate(0.5deg);
    }
    46% {
      transform: translate(1.5px, -0.5px) rotate(-0.5deg);
    }
    48% {
      transform: translate(2.5px, -0.5px) rotate(0.5deg);
    }
    50% {
      transform: translate(-1.5px, 1.5px) rotate(0.5deg);
    }
    52% {
      transform: translate(-0.5px, 1.5px) rotate(0.5deg);
    }
    54% {
      transform: translate(-1.5px, 1.5px) rotate(0.5deg);
    }
    56% {
      transform: translate(0.5px, 2.5px) rotate(1.5deg);
    }
    58% {
      transform: translate(2.5px, 2.5px) rotate(0.5deg);
    }
    60% {
      transform: translate(2.5px, -1.5px) rotate(1.5deg);
    }
    62% {
      transform: translate(-1.5px, 0.5px) rotate(1.5deg);
    }
    64% {
      transform: translate(-1.5px, 1.5px) rotate(1.5deg);
    }
    66% {
      transform: translate(0.5px, 2.5px) rotate(1.5deg);
    }
    68% {
      transform: translate(2.5px, -1.5px) rotate(1.5deg);
    }
    70% {
      transform: translate(2.5px, 2.5px) rotate(0.5deg);
    }
    72% {
      transform: translate(-0.5px, -1.5px) rotate(1.5deg);
    }
    74% {
      transform: translate(-1.5px, 2.5px) rotate(1.5deg);
    }
    76% {
      transform: translate(-1.5px, 2.5px) rotate(1.5deg);
    }
    78% {
      transform: translate(-1.5px, 2.5px) rotate(0.5deg);
    }
    80% {
      transform: translate(-1.5px, 0.5px) rotate(-0.5deg);
    }
    82% {
      transform: translate(-1.5px, 0.5px) rotate(-0.5deg);
    }
    84% {
      transform: translate(-0.5px, 0.5px) rotate(1.5deg);
    }
    86% {
      transform: translate(2.5px, 1.5px) rotate(0.5deg);
    }
    88% {
      transform: translate(-1.5px, 0.5px) rotate(1.5deg);
    }
    90% {
      transform: translate(-1.5px, -0.5px) rotate(-0.5deg);
    }
    92% {
      transform: translate(-1.5px, -1.5px) rotate(1.5deg);
    }
    94% {
      transform: translate(0.5px, 0.5px) rotate(-0.5deg);
    }
    96% {
      transform: translate(2.5px, -0.5px) rotate(-0.5deg);
    }
    98% {
      transform: translate(-1.5px, -1.5px) rotate(-0.5deg);
    }
    0%, 100% {
      transform: translate(0, 0) rotate(0deg);
    }
  }
</style>