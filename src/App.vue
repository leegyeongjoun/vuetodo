<template>
    <nav class="navbar">
       <ul>
            <li><router-link to="/">HOME</router-link></li>
            <li><router-link to="/todos">TODOS</router-link></li>
            <!--  이름으로 불러오고 싶을 때
                <li><router-link :to="{name:'Home'}">HOME</router-link></li>
                <li><router-link :to="{name:'Todos'}">TODOS</router-link></li> 
            -->
       </ul>
    </nav>
    <router-view/>
</template>

<script>
    export default {
        
    }
</script>

<style>
    .navbar{border: 1px solid #ddd; widows: 100%; padding: 20px;}
    .navbar>ul{list-style-type: none; margin: 0 auto; max-width: 1024px; width: 100%; display: flex;}

    .navbar ul li a{text-decoration: none; padding: 10px 20px; font-weight: bold; color: skyblue;}
</style>