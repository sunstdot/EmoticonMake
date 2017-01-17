<template>
    <div class="magic_area">
        <div class="setting_tab">
            <span 
                class="tab_item"
                v-for="tab in tabs"
                :class="{'tab_active': $index === selected}"
                @click="choose($index)">{{tab.tabName}}</span>
        </div>
        <component :is="currentView" transition="fade" transition-mode="out-in"></component>

    </div>
</template>

<script> 
import Basicset from './Basicset.vue'
import Advanceset from './Advanceset.vue'
import Tuyaset from './Tuyaset.vue'

export default{
    data(){
        return{
            tabs:[
                {tabName:"图片效果"},
                {tabName:"高级效果"},
                {tabName:"表情涂鸦"}
            ],
            selected:0,
            currentView:'set_tab_0'
        }
    },
    methods:{
        choose(index){
            this.selected = index;
            this.currentView = 'set_tab_' + index;
        }
    },
    components:{
        'set_tab_0':Basicset,
        'set_tab_1':Advanceset,
        'set_tab_2':Tuyaset
    }
}
</script>
