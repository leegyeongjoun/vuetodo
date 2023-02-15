<template>
    <!--todos에 내용 하나하나를 todo가 가져옴  -->
    <div>
        <div v-for="(todo, index) in todos" :key="todo.id" class="card">
            <div class="card-body">
                <div class="form-check">                    
                    <!-- <label class="form-check-label" :style="todo.completed ? todoStyle : {}"> -->
                    <label class="form-check-label" :class="{todoStyle:todo.completed}">
                        <!-- 부모한게 내려받는 값은 바뀌면 안됨 v-model="todo.completed" -->
                        <input type="checkbox" class="form-check-input" :checked="todo.completed" @change="toggleTodo(index)">
                        {{ todo.subject }}
                    </label>
                </div>
                <div>
                    <button class="btnR" @click="deleteTodo(index)">삭제</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            todos: {
                type: Array,
                required: true
            }
        },
        // 이벤트 전달 할 때
        emits: ['toggle-todo', 'delete-todo'],
        setup(props, {emit}){//emit을 구조분해할당 context하면 emit앞에 context
            const toggleTodo = (index) => {
                // 부모에게 다시 보내기 (바뀌는 값을 보내줘야하기 때문에)
                emit('toggle-todo', index)
                // 내가 선택하는 친구의 인덱스 값을 가지고 있고 true false값으로 받아오기 위해서 
            }
            const deleteTodo = (index) => {
                emit('delete-todo', index)
            }
            return {
                 deleteTodo,
                 toggleTodo
            }
        }  
    }  
</script>
<style>
    
</style>