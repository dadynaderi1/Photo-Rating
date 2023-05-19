<script>
import FileBrowser from './components/FileBrowser.vue'

export default{
  name:'App',
  components:{FileBrowser}
}

</script>

<template>
  <div class="wrapper">
    <FileBrowser/>
 
</div>
</template>

<style>
@import "https://fonts.googleapis.com/css?family=Poppins:300,400,500,600,700";
body {
    font-family: 'Poppins', sans-serif;
    background: #fafafa;
}
.wrapper {
    display: flex;
    width: 100%;
    align-items: stretch;
}

</style>
