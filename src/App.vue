<template>
<div>

 <TodoList/>
 </div>
</template>

 

<script>
import TodoList from "./components/TodoList.vue"
export default {
name:'App',
components:{
TodoList
}
}
</script>

<style>

body{
background-color: rgb(66, 66, 66);
color:aqua;
}
</style>