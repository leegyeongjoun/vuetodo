<template>
    <div class="container">
        <h1>Todo Page</h1>
        <div v-if="loading">
            Loading...
        </div>
        <form v-else @submit.prevent="onSave">
            <div class="row1">
                <div class="form-group">
                    <div>
                        <label>Todo Subject</label>
                        <!-- v-model을 사용하여 양방향 통신을 하면 선택한 친구의 subject값이 들어온다. -->
                        <input type="text" class="form-control mtp" v-model="todo.subject">
                    </div>
                </div>
                <div class="form-group">
                    <label>Status</label>
                    <div>
                        <button @click.prevent="toggleTodoStatus" type="submit" class="btn" :class="todo.completed ? 'btnGG':'btnRL'">{{todo.completed ? '완료':'미완료'}}</button>
                    </div>
                </div> 
            </div>
            <button class=" mt" type="submit" :disabled="!todoUpdated">저장</button>
            <button class="btn btnW btnL" @click="movieToTodoListPage">취소</button>
        </form>
    </div>
</template>

<script>
    import { useRoute,useRouter } from 'vue-router'
    import axios from 'axios';
    import { ref, computed } from 'vue';
    import _ from 'lodash'
    export default {
        setup(){
            // 클릭하면 이동하게 하려고
            const route=useRoute();
            const router=useRouter();
            const todo=ref('');
            const loading = ref(true);
            const originalTodo = ref('');
            const todoId=route.params.id;
            // console.log(route.params.id)
            const getTodo = async () => {
                const res = await axios.get(`http://localhost:3000/todos/${todoId}` );
                todo.value = res.data;
                originalTodo.value = res.data;
                loading.value=false;
            }
            
            const toggleTodoStatus = () => {
                todo.value.completed = !todo.value.completed;
            }
            // 취소버튼을 눌르면 TodoList페이지로 가게하기위해
            const movieToTodoListPage = () => {
                router.push({
                    name:'Todos'
                })
            }
            getTodo();
            // 미완료된걸 완료로 바꾸고 저장버튼을 눌르면 바뀜
            const onSave = async () => {
                const res = await axios.put(`http://localhost:3000/todos/${todoId}`, {
                    subject:todo.value.subject,
                    completed:todo.value.completed, 
                })
                console.log(res)
            }
            // 기존값과 이전값
            const todoUpdated = computed(() => {
                // 같지않다면 값을 바꾸기
                return !_.isEqual(todo.value, originalTodo.value)
            }) 
            return{
                todo,
                getTodo,
                toggleTodoStatus,
                movieToTodoListPage,
                onSave,
                todoUpdated
            }
        }
    }
</script>
<style scoped>
    h1{margin-top: 30px; margin-bottom: 20px;}
    .form-group{display: flex; flex-direction: column; margin-top: 7px;}
    .mtp{padding: 11px 20px; margin-top: 5px;}
    .form-group label{font-weight: bold; margin-bottom: 5px;}
    .btnmt{margin-top: 5px;}
    .row1{display: flex; justify-content: space-between;}
    .row1>div{flex-basis: 49%;}
    .btnRL{padding: 10px 20px; background: red;}
    .btnGG{padding: 10px 20px; background: green;}
    .btnW{background: rgb(201, 201, 201); color: #222;;}
    .btnL{margin-left: 10px;}
</style>