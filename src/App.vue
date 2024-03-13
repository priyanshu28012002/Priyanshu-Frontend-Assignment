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
background-color: rgb(71, 88, 105);
color:rgb(239, 209, 209);
}
</style>