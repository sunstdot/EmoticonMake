<template>
    <div class="container">
    <div class="row">
      <div class="col-md-9">
        <div class="img-container" id="drop_img">
          <img src="../static/image/img.png" alt />
        </div>
      </div>
      <div class="col-md-3">
        <div class="docs-preview clearfix" style="min-height:250px;">
          <div class="img-preview preview-lg" id="drop_lg_img"></div>
          <div class="img-preview preview-md"></div>
          <div class="img-preview preview-sm"></div>
          <div class="img-preview preview-xs"></div>
        </div>
        <div class="btn-group btn-group-justified" data-toggle="buttons">
          <label class="btn btn-primary" data-method="setAspectRatio" data-option="1.7777777777777777" title="Set Aspect Ratio">
            <input class="sr-only" id="aspestRatio1" name="aspestRatio" value="1.7777777777777777" type="radio">
            <span class="docs-tooltip" data-toggle="tooltip" title="$().cropper(&quot;setAspectRatio&quot;, 16 / 9)">
              16:9
            </span>
          </label>
          <label class="btn btn-primary" data-method="setAspectRatio" data-option="1.3333333333333333" title="Set Aspect Ratio">
            <input class="sr-only" id="aspestRatio2" name="aspestRatio" value="1.3333333333333333" type="radio">
            <span class="docs-tooltip" data-toggle="tooltip" title="$().cropper(&quot;setAspectRatio&quot;, 4 / 3)">
              4:3
            </span>
          </label>
          <label class="btn btn-primary" data-method="setAspectRatio" data-option="1" title="Set Aspect Ratio">
            <input class="sr-only" id="aspestRatio3" name="aspestRatio" value="1" type="radio">
            <span class="docs-tooltip" data-toggle="tooltip" title="$().cropper(&quot;setAspectRatio&quot;, 1 / 1)">
              1:1
            </span>
          </label>
          <label class="btn btn-primary" data-method="setAspectRatio" data-option="0.6666666666666666" title="Set Aspect Ratio">
            <input class="sr-only" id="aspestRatio4" name="aspestRatio" value="0.6666666666666666" type="radio">
            <span class="docs-tooltip" data-toggle="tooltip" title="$().cropper(&quot;setAspectRatio&quot;, 2 / 3)">
              2:3
            </span>
          </label>
          <label class="btn btn-primary active" data-method="setAspectRatio" data-option="NaN" title="Set Aspect Ratio">
            <input class="sr-only" id="aspestRatio5" name="aspestRatio" value="NaN" type="radio">
            <span class="docs-tooltip" data-toggle="tooltip" title="$().cropper(&quot;setAspectRatio&quot;, NaN)">
              Free
            </span>
          </label>
        </div>

        <div class="dropdown dropup docs-options" style="display:block;margin-top:20px;">
          <button type="button" class="btn btn-primary btn-block dropdown-toggle" data-widget-ele="importImg">
            导入工作区
          </button>
          <button type="button" class="btn btn-primary btn-block dropdown-toggle" data-widget-ele="saveImg">
            保存图片
          </button>
        </button>
        </div>
      </div>
    </div>
  </div>
</template>
