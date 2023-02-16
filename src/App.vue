<template>
    <nav class="navbar">
       <ul>
            <li><router-link to="/">HOME</router-link></li>
            <li><router-link to="/todos">TODOS</router-link></li>
       </ul>
    </nav>
    <router-view/>
</template>

<script>
    export default {
        
    }
</script>

<style>
    .navbar{}
    div{width: 100%; margin: 0 auto;}
    ul{display: flex; list-style: none; align-items:flex-start; justify-content: center;}
    li{margin-left: 10px;}
    a{text-decoration: none; color: black; font-size: 20px;}
</style>