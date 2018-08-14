<template>
  <div id="app">


    <div @mousedown.stop="cancleEL" style="height: 500px; width: 500px; margin: 20px; border: 1px solid red; position: relative;">

      <div @mousedown.stop="selectEl($event,index)" v-for="(el,index) in json" 
      :style="'height: '+el.height+'px; width: '+el.width+'px; border: 1px solid red; position: absolute; left:'+el.left+'px; top: '+el.top+'px; transform: rotate(' + el.rotateAngle + 'deg)'">
        {{el.content}}
      </div>

      <VueDraggableResizable :x="p.x" :y="p.y" :w="p.w" :h="p.h"  v-if="showDrage" @resizing="onResizeing" @dragging="onDragging" @rotating="onRotating">
      </VueDraggableResizable>
    </div>

  </div>
</template>

<script>
import VueDraggableResizable from './components/vue-draggable-resizable'

export default {
  name: 'app',
    data(){
     return {
        showDrage:false,
        json:[
        {id: '001001', type: 1, edite: false, content: "你要编辑的文字", top: 43, left: 40, width: 300, height: 42,rotateAngle:0},
         {id: '002002', type: 1, edite: false, content: "你要编辑的文字", top: 143, left: 140, width: 300, height: 142,rotateAngle:0},
       {id: '002003', type: 1, edite: false, content: "你要编辑的文字", top: 380, left: 40, width: 200, height: 111,rotateAngle:0},
        ],
       p:{
        x:0,
        y:0,
        w:0,
        h:0
       },
       active:false,
       activeGronp:[]
        
      }
  },
   mounted:function() {
       
    },

  methods:{

    onResizeing(l,t,w,h){
          this.json[this.index].top = t
          this.json[this.index].left = l
          this.json[this.index].width= w
          this.json[this.index].height= h
    },

    onDragging(x,y){
           this.json[this.index].top = y
          this.json[this.index].left = x
     
    },

    onRotating(r){
         this.json[this.index].rotateAngle = r
    },

    cancleEL(){
      this.showDrage =false
    },

    selectEl($event,index){

      console.log($event)

      this.index = index


      var itemJSON = this.json[index];
      this.showDrage = true;
      this.active = index

      //按住shit
      
      if($event.shiftKey){
        if(this.activeGronp.indexOf(itemJSON.id) < 0)
        {this.activeGronp.push(itemJSON.id)}
      }



      this.p.w = itemJSON.width
      this.p.h = itemJSON.height
      this.p.x = itemJSON.left
      this.p.y = itemJSON.top
      this.p.rotateAngle = itemJSON.rotateAngle

    },
   
      //计算组的大小
     computedGpSize() {
      var gWidthArr=[],gHeightArr = [];

     var positionArr = this.activeGronp.map((item)=>{

          console.group('this.activeGronp.map',item)

          for (var k in this.json){
            if(this.json[k].id == item){

              return {
                x:this.json[k].left,
                y:this.json[k].top,
                w:this.json[k].width,
                h:this.json[k].height
              }
            }
          }



      })

     let position ={}
     positionArr.forEach((po)=>{

        if(typeof position.x === 'undefined'){
          position.x = po.x
          position.y = po.y 
        }else{
          position.x = po.x >= position.x ? position.x :po.x
          position.y = po.y >=position.y ? position.y :po.y
        }
      gWidthArr.push(po.w+po.x-position.x)
      gHeightArr.push(po.h+po.y-position.y)
     })

     position.w = Math.max.apply(null, gWidthArr)
     position.h = Math.max.apply(null, gHeightArr)
     this.p = position
    }
  },
  watch:{
    activeGronp:function(val){


     if(this.activeGronp.length>1){

       this.computedGpSize()
     }



    }
  },
  components: {
    VueDraggableResizable
  }
}
</script>

<style>
  .active {
    border: 1px dashed black;
  }
  h2 {
    opacity: 0.3;
    padding: 0 0 0 20px;
    z-index: -1;
  }
  .dragHandle {
    position: absolute;
    top: 0;
    right: 0;
    padding: 5px;
    font-size: 20px;
    background-color: lightgrey;
  }

</style>
