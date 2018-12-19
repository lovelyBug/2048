<template>
  <div id="app">
    <ul class="wrap">
      <li
        v-for="(item, index) in 16"
        :key="index"
        class="box" />
    </ul>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app ul {
  list-style:none;
  display:inline-block;
  margin:0;
  padding:0;
  position:relative;
  height:7.5rem;
  width:100%;
  border: 1px solid #ccc;
}
#app ul li {
  float: left;
  width: 25%;
  height: 25%;
  line-height:200%;
  text-align:center;
  cursor:pointer;
  background: hsla(30,37%,89%,.35);
  border: 1px solid #ccc;
  box-shadow: 1px 1px 4px;
  box-sizing: border-box;
  -webkit-user-select: none;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
