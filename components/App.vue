<template>
    <div class="wrapper">
        <div class="nav"><span class="red_font">Expression</span>Factory</div>
        <div class="content_wrap">
            <div class="main_content">
                <Headtool></Headtool>
                <Editarea></Editarea>
            </div>
            <Sidertool></Sidertool>
        </div>
    </div>
</template>

<script>
import Sidertool from './Sidertool.vue'
import Headtool from './Headtool.vue'
import Editarea from './Editarea.vue'

export default {    
  components: {
    Sidertool,
    Headtool,
    Editarea
  }
}
</script>
