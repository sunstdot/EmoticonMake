<template>
    <div class="tab_body tab_body1">
        <div class="item_content">
            <div class="basic_row" v-bind:class="[filterType === 'origin' ? 'selected' :'']" @click="basicfunc('origin')">
                <span class="left_txt">原图效果：</span>
                <img src="../static/image/img.png" class="img_wrapper" />
            </div>
            <div class="basic_row" v-bind:class="[filterType === 'grayscale' ? 'selected' :'']" @click="basicfunc('grayscale')">
                <span class="left_txt">灰度效果：</span>
                <img src="../static/image/img1.png" class="img_wrapper"/>
            </div>
            <div class="basic_row" v-bind:class="[filterType == 'emboss' ? 'selected' :'']" @click="basicfunc('emboss')">
                <span class="left_txt">浮雕效果：</span>
                <img src="../static/image/img2.png" class="img_wrapper"/>
            </div>
            <div class="basic_row" v-bind:class="[filterType == 'sharpen' ? 'selected' :'']" @click="basicfunc('sharpen')">
                <span class="left_txt">锐化效果：</span>
                <img src="../static/image/img3.png" class="img_wrapper" />
            </div>
            <div class="basic_row" v-bind:class="[filterType == 'invert' ? 'selected' :'']" @click="basicfunc('invert')">
                <span class="left_txt">反相效果：</span>
                <img src="../static/image/img4.png" class="img_wrapper" />
            </div>
            <div class="basic_row" v-bind:class="[filterType == 'pixelate' ? 'selected' :'']" @click="basicfunc('pixelate')">
                <span class="left_txt">马赛克效果：</span>
                <img src="../static/image/img5.png" class="img_wrapper" />
            </div>
        </div>
    </div>
</template>
<script>
    export default{
        data(){
            return {
		        f:fabric.Image.filters,
                filterType:'origin'
            }
        },
        watch:{
            
        },
        methods:{
            applyFilter(indx,filter){
                let obj = canvas.getActiveObject() ?canvas.getActiveObject():canvas.getActiveGroup();
                obj.filters[index] = filter;
                obj.applyFilters(canvas.renderAll.bind(canvas));
	        },
            basicfunc(type){
                let obj = canvas;
                let objects = canvas._objects;
                let target,filter;
                target = canvas.getActiveObject();
                if(!target){
                    for(let i=0;i<objects.length;i++){
                        if(objects[i].type === 'image'){
                            target = objects[i];
                        }
                    }
                }
                
                if(!target){
                    return;
                }
                this.filterType = type;
                if(type === 'origin'){
                    target.filters = [];
                }else{
                    if(type==="grayscale"){	
                        filter = new fabric.Image.filters.Grayscale();
                    }else if(type === 'pixelate'){
                        filter = new fabric.Image.filters.Pixelate(); 	
                    }else if(type === 'emboss'){
                        //浮雕
                        filter = new this.f.Convolute({
                            matrix:[
                                1,1,1,
                                1,0.7,-1,
                                -1,-1,-1
                            ]
                        });    
                    }else if(type === 'sharpen'){
                        //锐化
                        filter = new this.f.Convolute({
                            matrix:[
                                0,-1,0,
                                -1,5,-1,
                                0,-1,0
                            ]        
                        });
                    }else if(type==="invert"){	
                        //反相
                        filter = new this.f.Invert();
                    }
                    target.filters = [];
                    target.filters.push(filter);
                }
                target.applyFilters(canvas.renderAll.bind(canvas));
            }
        }
    }
</script>
