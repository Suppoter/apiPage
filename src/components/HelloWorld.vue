<template>
  <draggable v-model="myArray" :options="{group:'people'}" @start="drag=true" @end="drag=false">
    <div v-for="element in myArray" :key="element.id">{{element.name}}</div>
  </draggable>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      myArray: [
        {name:"John", id:1},
        {name:"Joao", id:2},
        {name:"Jean", id:3},
        {name:"Gerard", id:4}
      ],
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
