<template>
  <div>
    <p class="home-bottom-message">
      
      <img src="https://cdn.discordapp.com/attachments/1063807822959235082/1071805385851281428/img.png.png"/> 
    </p>
  </div>
</template>

<script>

import { mapActions, mapGetters } from 'vuex';

export default {
  name: 'app-home-page',


  created() {
    if (this.isMobile) {
      this.collapseMenu();
    }
  },
  computed: {
    ...mapGetters({
      isMobile: 'layout/isMobile',
    })
  },

  methods: {
    ...mapActions({
      collapseMenu: 'layout/collapseMenu',
    })
  }
};
</script>

<style scoped>
.el-col {
  margin-bottom: 16px;
}

.home-bottom-message {
  width: 100%;
  text-align: center;
  color: grey;
}
@import '../../../../public/input.css';
</style>
