<template>
  <div class="">
    <SuperuserProfile :style="screenPadding" />
    
  </div>
</template>

<script>
// @ is an alias to /src
import SuperuserProfile from '@/components/SuperuserProfile.vue'
 

export default {
  name: 'SuperuserProfileView',
  components: {
    SuperuserProfile,
     
  },

  data() {
      return {
        width: window.innerWidth,
        screenPadding: this.$superuserBigScreenPadding,
      }
    },

  methods: {
    onResize() {
      this.width = window.innerWidth;
      this.changePadding()
    },
    changePadding(){
      if(this.width < this.$smallScreenSize){
        this.screenPadding = this.$superuserSmallScreenPadding;
        return;
      }
      this.screenPadding = this.$superuserBigScreenPadding;
    }
  },
  beforeMount(){
    this.changePadding()
  },
  created() {
    window.addEventListener("resize", this.onResize);
  },
  destroyed() {
    window.removeEventListener("resize", this.onResize);
  },
}
</script>
