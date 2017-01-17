<template>
    <div class="btn_area">
        <span class="btn_item importImg mr40">
          <input type="file" id="addImg" class="addImg" @change="setImg()">
        </span>
        <span class="btn_item mr40" @click="rasterize()"><i class="fa fa-sign-out mr10"></i>导出</span>
        <span class="btn_item mr40" @click="addText()"><i class="fa fa-text-width mr10"></i>文字</span> 
        <span class="btn_item mr40" @click="drop()"><i class="fa fa-crop mr10"></i>裁剪</span>
        <span class="btn_item mr40" @click="clearCanvas()"><i class="fa fa-trash mr10"></i>清空</span>    
    </div>
	<div class="modal fade bs-example-modal-lg in" id="modalBox">
      <div class="modal-backdrop fade in" style="z-index:0;"></div>
	  <div class="modal-dialog modal-lg" style="width:1200px;height:540px;">
	    <div class="modal-content" style="width:1200px;height:540px;padding:20px 0;">
            <div class="modal-header" style="padding:0;">
              <button type="button" class="close" @click="closeModal()" style='margin:-15px 13px 10px 0;'>
                <span aria-hidden="true">×</span><span class="sr-only">Close</span>
              </button>
            </div>
	    	<div class="modal-body" id="drop_canvas" style="padding:10px 15px;"></div>
	    </div>
	  </div>
	</div>
</template>
<script>
    import DropModal from './DropModal.vue'
    export default{
        data(){
            return {
               zIndex:1,
               modalImg:null,
               modalImgw:null,
               modalImgh:null,
               modalPleft:null,
               modalPtop:null,
               modalCanvas:null,
               modalCropw:null,
               modalCroph:null,
               modalCtx:null
            }
        },
        components: {
            DropModal
        },
        methods:{
            //清空画布
            clearCanvas(){
              canvas.clear();
            },
            //添加文字
            addText(){
                let text = new fabric.IText('此处编辑文字',{
                    left: 10,
                    top: 20,
                    fontFamily: 'Courier',
                    fill: '#000000',
                    type:'font',
                    fontSize:16,
                    textAlign:'center',
                    isEditing:true,
                    styles:{
                    }
                });
                canvas.add(text);
            },
            //导入图片
            setImg(){
                var editArea = document.getElementById('editArea').style.backgroundImage="url()";
                var imgUrl = this.getObjectURL(document.getElementById('addImg').files[0]);
                var randomVal1 = Math.random();
                var randomVal2 = Math.random();
                this.addImage(imgUrl,Math.min(randomVal1, randomVal2),Math.max(randomVal1, randomVal2));
            },
            //运用fabric把图片做成随机大小导入canvas画布
            addImage(imgAdd, minScale, maxScale) {
                var _this = this;
                //var coord = _this.getRandomLeftTop();
                fabric.Image.fromURL(imgAdd, function(image) {
                  image.set({
                    left: 0,
                    top: 0,
                    type: 'image'
                  })
                  .scale(_this.getRandomNum(minScale, maxScale))
                  .setCoords();
                  canvas.add(image);
                });
            },
            //导入时获取图片本地路径
            getObjectURL(file) {
                var url = null;
                if (window.createObjectURL != undefined) {
                    url = window.createObjectURL(file)
                } else if (window.URL != undefined) {
                    url = window.URL.createObjectURL(file)
                } else if (window.webkitURL != undefined) {
                    url = window.webkitURL.createObjectURL(file)
                }
                return url
            },
            getRandomNum(min, max) {
                return Math.random() * (max - min) + min;
            },
            getRandomLeftTop() {
                var offset = 50;
                return {
                    left: fabric.util.getRandomInt(0 + offset, 700 - offset),
                    top: fabric.util.getRandomInt(0 + offset, 500 - offset)
                };
            },
            //导出并下载图片
            rasterize() {
                var _this = this;
                if (!fabric.Canvas.supports('toDataURL')) {
                    alert('This browser doesn\'t provide means to serialize canvas to an image');
                }else {
                    window.open(canvas.toDataURL('png'));
                    _this.saveAs(canvas.toDataURL('png'),"new.png");
                }
            },
            //裁剪图片
            drop(){
                var canvasData = canvas.toDataURL('png');
                document.getElementById('drop_canvas').innerHTML = DropModal.template;
                document.getElementById('drop_img').innerHTML = '<img src='+ canvasData +' style="width:100%;height:100%">';
                document.getElementById('modalBox').style.display = 'block';
                this.handleDrop();
                this.handleClick();
            },
            //关闭裁剪框
            closeModal(){
                document.getElementById('modalBox').style.display = 'none';
            },
            //裁剪图片时临时保存的一些数据 宽高
            handModalImg(){
              var _this = this;
              var $image = $('.img-container > img');
              var url = $image.eq(0).attr("src");
              var canvasdata = $image.cropper("getCanvasData");
              var cropdata = $image.cropper('getCropBoxData');
              _this.modalCropw = cropdata.width; // 剪切的宽
              _this.modalCroph = cropdata.height; // 剪切的宽
              _this.modalImgw = canvasdata.width; // 图片缩放或则放大后的高
              _this.modalImgh = canvasdata.height; // 图片缩放或则放大后的高
              
              _this.modalPleft = canvasdata.left - cropdata.left; // canvas定位图片的左边位置
              _this.modalPtop = canvasdata.top - cropdata.top; // canvas定位图片的上边位置
              
              var canvas = document.createElement("canvas");
              _this.modalCtx = canvas.getContext('2d');
              
              canvas.width = cropdata.width;
              canvas.height = cropdata.height;
              
              var img = new Image();
              img.src = url;
              _this.modalImg = img;
              _this.modalCanvas = canvas;
            },
            //裁剪数据绑定的方法
            handleClick(){
                var _this = this; 
                $(document).undelegate("[data-widget-ele=importImg]","click");
                $(document).delegate("[data-widget-ele=importImg]","click",function(){
                    _this.handModalImg();
                    _this.modalImg.onload = function() {
                        this.width = _this.modalImgw;
                        this.height = _this.modalImgh;
                        _this.modalCtx.drawImage(this, _this.modalPleft, _this.modalPtop, this.width, this.height);
                        var base64 = _this.modalCanvas.toDataURL('image/jpg', 1);  
                        window.canvas.clear();
                        fabric.Image.fromURL(base64, function(image) {
                          image.set({
                            left: 0,
                            top: 0,
                            width:_this.modalCropw,
                            height:_this.modalCroph,
                            type: 'image'
                          })
                          .setCoords();
                          window.canvas.add(image);
                        });
                    }
                    document.getElementById('modalBox').style.display = 'none';
                });
                $(document).undelegate("[data-widget-ele=saveImg]","click");
                $(document).delegate("[data-widget-ele=saveImg]","click",function(){
                    _this.handModalImg();
                    _this.modalImg.onload = function() {
                        this.width = _this.modalImgw;
                        this.height = _this.modalImgh;
                        _this.modalCtx.drawImage(this, _this.modalPleft, _this.modalPtop, this.width, this.height);
                        var base64 = _this.modalCanvas.toDataURL('image/jpg', 1);
                        var preWindow = window.open(base64);
                        _this.saveAs(base64,"new.png");
                    }
                });
            },
            saveAs(Url,filename){
                  var blob=new Blob([''], {type:'application/octet-stream'});
                  var url = URL.createObjectURL(blob);
                  var a = document.createElementNS("http://www.w3.org/1999/xhtml",'a');
                  a.href = Url;
                  a.download = filename;
                  var e = document.createEvent('MouseEvents');
                  e.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
                  a.dispatchEvent(e);
                  URL.revokeObjectURL(url);
            },
            //缩放，裁剪，比例
            handleDrop(){
                var $image = $('.img-container > img'),
                $dataX = $('#dataX'),
                $dataY = $('#dataY'),
                $dataHeight = $('#dataHeight'),
                $dataWidth = $('#dataWidth'),
                $dataRotate = $('#dataRotate'),
                //设置初始参数
                options = {
                  aspectRatio: NaN,   //设置剪裁容器的比例
                  preview: '.img-preview', //添加额外的元素（容器）的预览
                  crop: function (data) {  //当改变剪裁容器或图片时的事件函数
                    $dataX.val(Math.round(data.x));
                    $dataY.val(Math.round(data.y));
                    $dataHeight.val(Math.round(data.height));
                    $dataWidth.val(Math.round(data.width));
                    $dataRotate.val(Math.round(data.rotate));
                  }
                };
                $image.on({
                  'build.cropper': function (e) { //当cropper对象开始加载图片的时候触发该事件
                    console.log(e.type);
                  },
                  'built.cropper': function (e) {  //当cropper对象构建完成时触发该事件
                    console.log(e.type);
                  },
                  'dragstart.cropper': function (e) {
                    console.log(e.type, e.dragType);
                  },
                  'dragmove.cropper': function (e) { //当剪裁区域发生改变时触发
                    console.log(e.type, e.dragType);
                  },
                  'dragend.cropper': function (e) {
                    console.log(e.type, e.dragType);
                  },
                  'zoomin.cropper': function (e) { //当cropper对象开始放大canvas时触发
                    console.log(e.type);
                  },
                  'zoomout.cropper': function (e) { //当cropper对象开始缩小canvas时触发
                    console.log(e.type);
                  }
                }).cropper(options);

                // Methods
                $(document.body).on('click', '[data-method]', function () {
                  var data = $(this).data(),
                      $target,
                      result;

                  if (data.method) {
                    data = $.extend({}, data); // Clone a new one

                    if (typeof data.target !== 'undefined') {
                      $target = $(data.target);

                      if (typeof data.option === 'undefined') {
                        try {
                          data.option = JSON.parse($target.val());
                        } catch (e) {
                          console.log(e.message);
                        }
                      }
                    }

                    result = $image.cropper(data.method, data.option);

                    if (data.method === 'getCroppedCanvas') {
                      $('#getCroppedCanvasModal').modal().find('.modal-body').html(result);
                    }

                    if ($.isPlainObject(result) && $target) {
                      try {
                        $target.val(JSON.stringify(result));
                      } catch (e) {
                        console.log(e.message);
                      }
                    }

                  }
                }).on('keydown', function (e) {

                  switch (e.which) {
                    case 37:
                      e.preventDefault();
                      $image.cropper('move', -1, 0);
                      break;

                    case 38:
                      e.preventDefault();
                      $image.cropper('move', 0, -1);
                      break;

                    case 39:
                      e.preventDefault();
                      $image.cropper('move', 1, 0);
                      break;

                    case 40:
                      e.preventDefault();
                      $image.cropper('move', 0, 1);
                      break;
                  }
                });
            }
        }
    }
</script>
