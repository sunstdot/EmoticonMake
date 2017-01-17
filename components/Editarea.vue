<template>
  <div class="edit_area" id="editArea">
  	<canvas id="canvas" width="925" height="585"></canvas>
  </div>
</template>
<script type="text/javascript">
  window.onload = function() {
    window.canvas = new fabric.Canvas('canvas',{
      isDrawingMode:false
    });
    var copyCanvas = document.getElementById('copyCanvas');
    copyCanvas.addEventListener('click', function(e){
         var target = canvas.getActiveObject();
         if(target){
            target.moveTo(canvas._objects.length);
         }
    }, false);
  }
</script>
