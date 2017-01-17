<template>
    <div class="tab_body tab_body1">
        <div class="item_content">
            <div class="body_row">
                <div id="drawing-mode-options">
                    <div class="effect_adjust">
                        <span class="adjust_txt">&nbsp;开启涂鸦:</span>
                        <input type="checkbox" id="checkbox" v-model="modelChecked" style="margin-left:-60px;margin-top:8px;border-radius: 30px">
                    </div>
                    <div class="effect_adjust">
                        <span class="adjust_txt">&nbsp;&nbsp;&nbsp;&nbsp;涂鸦笔:</span>
                        <select style="color:black;margin-left:-60px;" v-model="selectMode">
                            <option v-for="mode in modes">
                                {{mode}}    
                            </option>                 
                        </select>
                    </div>
                    <div class="effect_adjust">
                        <span class="adjust_txt">&nbsp;&nbsp;线条颜色:</span>
                        <div class="colorInp-wrap">
                          <input type="color" value="#fff" v-model="lineColor" class="colorInp">
                        </div>
                    </div>
                    <div class="effect_adjust">
                        <span class="adjust_txt">&nbsp;&nbsp;阴影颜色:</span>
                        <div class="colorInp-wrap">
                            <input type="color" value="#005E7A" v-model="shadowColor" class="colorInp">
                        </div>
                    </div>
                    <div class="effect_adjust" >
                        <span class="adjust_txt">&nbsp;&nbsp;线条宽度:</span>
                        <span class="info dn">{{lineWidth}}</span>
                        <input type="range" value="30" min="0" max="50" v-model="lineWidth" title="{{lineWidth}}"> 
                    </div>
                    <div class="effect_adjust" title="{{shadowWidth}}">
                        <span class="adjust_txt">&nbsp;&nbsp;阴影宽度:</span>
                        <span class="info dn">{{shadowWidth}}</span>
                        <input type="range" value="0" min="0" max="50" v-model="shadowWidth">
                    </div>
                    <div class="effect_adjust" title="{{shadowOffset}}">
                        <span class="adjust_txt">阴影偏移量:</span>
                        <span class="info dn">{{shadowOffset}}</span>
                        <input type="range" value="0" min="0" max="50" v-model="shadowOffset">
                    </div>
                </div>
            </div>
            
        </div>
    </div>
</template>
<script>
    export default{
        data(){
            return {
                modes:["Pencil","Circle","Spray","Pattern","hline","vline","square","diamond"],
                brushPatterns:{},
                selectMode:'Pencil',
                lineWidth:15,
                shadowColor:"#005E7A",
                lineColor:"#005E7A",
                shadowWidth:0,
                shadowOffset:0,
                modelChecked:false
            }
        },
        ready(){
            
            canvas.isDrawingMode = this.modelChecked;
            if(fabric.PatternBrush){
               let vlinePatternBrush = new fabric.PatternBrush(canvas);
               vlinePatternBrush.getPatternSrc = function(){
                   let patternCanvas = fabric.document.createElement('canvas');
                   patternCanvas.width = patternCanvas.height = 10;
                   let ctx = patternCanvas.getContext('2d');
                   ctx.strokeStyle = this.lineColor;
                   ctx.lineWidth = 5;
                   ctx.beginPath();
                   ctx.moveTo(0,5);
                   ctx.lineTo(10,5);
                   ctx.closePath();
                   ctx.stroke();

                   return patternCanvas;
               } 
               this.brushPatterns.vline = vlinePatternBrush;

               let hLinePatternBrush = new fabric.PatternBrush(canvas);
               hLinePatternBrush.getPatternSrc = function(){
                   let patternCanvas = fabric.document.createElement('canvas');
                   patternCanvas.width = patternCanvas.height = 10;
                   let ctx = patternCanvas.getContext('2d');
                   ctx.strokeStyle = this.lineColor;
                   ctx.lineWidth = 5;
                   ctx.beginPath();
                   ctx.moveTo(5,0);
                   ctx.lineTo(5,10);
                   ctx.closePath();
                   ctx.stroke();
                   return patternCanvas;
               }
               this.brushPatterns.hline = hLinePatternBrush;

               let squarePatternBrush = new fabric.PatternBrush(canvas);
               squarePatternBrush.getPatternSrc = function(){
                   let squareWidth = 10,squareDistance = 2;
                   let patternCanvas = fabric.document.createElement('canvas');
                   patternCanvas.width = patternCanvas.height = squareWidth+squareDistance;
                   let ctx = patternCanvas.getContext('2d');
                   ctx.fillStyle = this.lineColor;
                   ctx.fillRect(0,0,squareWidth,squareWidth);
                   return patternCanvas;
               }

               this.brushPatterns.square = squarePatternBrush;

               let diamondPatternBrush = new fabric.PatternBrush(canvas);
               diamondPatternBrush.getPatternSrc = ()=>{
                   let squareWidth = 10,squareDistance = 5;
                   let patternCanvas = fabric.document.createElement('canvas');
                   let rect = new fabric.Rect({
                       width:squareWidth,
                       height:squareWidth,
                       angle:45,
                       fill:this.lineColor
                   });
                   
                   var canvasWidth = rect.getBoundingRectWidth();
                   patternCanvas.width = patternCanvas.height = canvasWidth + squareDistance;
                   rect.set({left:canvasWidth / 2, top: canvasWidth / 2});

                   var ctx = patternCanvas.getContext('2d');
                   rect.render(ctx);
                   return patternCanvas; 
               };          
               this.brushPatterns.diamond = diamondPatternBrush;
            }
            if(canvas.freeDrawingBrush){
                canvas.freeDrawingBrush.color = this.lineColor;
                canvas.freeDrawingBrush.width = parseInt(this.lineWidth,10) || 1;
                canvas.freeDrawingBrush.shadowBlur = 0;
            }

        },
        watch:{
            selectMode(value){
                console.log("-------"+value);
                if(this.brushPatterns[value]){
                    canvas.freeDrawingBrush = this.brushPatterns[value];
                }else{
                    canvas.freeDrawingBrush = new fabric[value + 'Brush'](canvas);
                }
                if(canvas.freeDrawingBrush){
                    canvas.freeDrawingBrush.color = this.lineColor;
                    canvas.freeDrawingBrush.width = parseInt(this.lineWidth,10) || 1;
                    canvas.freeDrawingBrush.shadowBlur = parseInt(this.shadowWidth,10) || 0;
                }
            },
            lineWidth(value){
                canvas.freeDrawingBrush.width = parseInt(this.lineWidth,10);
            },
            lineColor(value){
                canvas.freeDrawingBrush.color = this.lineColor;
            },
            shadowColor(value){
                canvas.freeDrawingBrush.shadowColor = this.shadowColor;
            },
            shadowWidth(value){
                canvas.freeDrawingBrush.shadowWidth = this.shadowWidth;
            },
            shadowOffset(value){
                canvas.freeDrawingBrush.shadowOffsetX = canvas.freeDrawingBrush.shadowOffsetY = parseInt(value,10) || 0;
            },
            modelChecked(){
                canvas.isDrawingMode = this.modelChecked;
            }    
        },
        methods:{
        }
    }
</script>