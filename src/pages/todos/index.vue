<template>
    <div class="container">
        <h2>TODO-LIST</h2>
        <input type="text" placeholder="검색" class="form-control" v-model="searchText">
        <TodoSimpleForm @add-todo="addTodo"/>
        <div style="textAlign:center" v-if="!todos.length">
            찾는 문장이 없습니다.
        </div>
        <!-- 내보낼이름 -->
        <TodoList :todos="todos" @delete-todo="deleteTodo" @toggle-todo="toggleTodo"/>
        <br>
        <nav class="nav">
            <ul class="pagination">
                <!-- @click="getTodos(currentPage-1)" 이전으로 가기 누르면 이전페이지 -->
                <li v-if="currentPage !== 1" class="page-item"><a @click="getTodos(currentPage-1)" href="#" class="page-link"><i class="fas fa-chevron-left"></i></a></li>
                <!-- 클릭하면 다른페이지로 가게 하는 것@click="getTodos(page)" -->
                <li v-for="page in numberofPages" :key="page" class="page-item" :class="currentPage===page ? 'active' : ''"><a href="#" @click="getTodos(page)" class="page-link" :class="currentPage===page ? 'active' : ''">{{ page }}</a></li>
                <!-- @click="getTodos(currentPage+1)" 다음으로 가기 누르면 다음페이지 -->
                <li class="page-item" v-if="numberofPages !== currentPage"><a href="#" @click="getTodos(currentPage+1)" class="page-link"><i class="fas fa-chevron-right"></i></a></li>
            </ul>
        </nav>
    </div>
</template>

<script>
// ref를 작성하려면 import 해줘야한다.
// v-model에 이름 작성 setup안에 todo 정의 import해오기 return에서 todo 다시 받기
// @는 src가 기준
import { ref, computed, watch } from 'vue';
import TodoSimpleForm from '@/components/TodoSimpleForm.vue';
import TodoList from '@/components/TodoList.vue';
import '@fortawesome/fontawesome-free/js/all.js'
import axios from 'axios';

export default {
    // 컴포넌트를 불러올 때
    components: {
        TodoSimpleForm,
        TodoList
    },
    setup(){
        // 배열구조로 todo를 가져옴 부모가 todo를 제어하고 있음
        // ref는 리액트 상태관리와 비슷 함
        const todos = ref([ ]);
        const searchText = ref('');
        const error = ref('');
        const limit = 5;//화면에 보일 일 갯수
        const numberOfTodos =  ref(0);//전체 갯수에있는 페이지
        const currentPage = ref(1);//현재 페이지
        const numberofPages = computed(()=>{//총 페이지3, computer는 +=과 비슷함
            return Math.ceil(numberOfTodos.value/limit)
        });

        // 새로고침해도 db.json 데이터를 받아오기
        const getTodos = async (page = currentPage.value) => {
            currentPage.value = page;
            try{
                const res = await axios.get(`http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`); //값이 들어올 땐 ?_page=7&_limit=20 로 작성 변수값을 들고오기 떄문에 ``을 사용하고 page는 ref이기 때문에 page만 적으면 안되고 value를 적어줘야함 _sort=id&_order=desc내림차순 &subject_like=${searchText.value}는 검색하는 친구 
                console.log(res.headers);// db.json에 들어있는 내용, heaers는 x-total-count: "10"는 안에 들어있는 데이터 갯수
                numberOfTodos.value=res.headers['x-total-count'];//-가 있어서 따옴표 안에 들어와야한다.
                todos.value=res.data;
            }catch(err){
                console.log(err);
                error.value="찾는 문장이 없습니다."
            }
        };
        getTodos();

        //  const addTodo = (todo) => {
        //      /* todos.value.push(todo) */
        //      error.value='';
        //      axios.post('http://localhost:3000/todos', {
        //          subject:todo.subject,
        //          completed:todo.completed,
        //          //동기는 123456을 요청하면 1번 해결되면 2번 ...
        //          //비동기는 123456을 요청하면 가장 빨리되는거 부터 먼저 보여 줌
        //      }).then((res)=>{
        //         console.log(res);
        //         todos.value.push(res.data)
        //      }).catch((err)=>{
        //         console.log(err);
        //         error.value="서버를 불러오지 못 했습니다."
        //      });
        //  }

        // 입력하면 db.json에 데이터가 저장됨
        const addTodo = async (todo) => {
            error.value='';
            try{
                await axios.post('http://localhost:3000/todos',{
                    subject:todo.subject,
                    completed:todo.completed,
                });
                getTodos(1);
            }catch(err){
                console.log(err);
                error.value="서버를 불러오지 못 했습니다."
            }         
        }
        
        //watch는 지정한 값이 변경된 시점에서 내가 원하는 액션(api call, route.push())을 하기 원할 때 사용합니다.
        // 모든 페이지에서 검색이 되게 하려면
        watch(searchText, ()=>{
            //console.log(current, prev) 입력한 현재값과 이전 값
            getTodos(1);
        })

        // const todoStyle={
        //     color:'gray',
        //     textDecoration:'line-through'
        // }
        
        // 할일의 목록을 지울 때
        const deleteTodo = async (index) => {
            error.value="";
            const id=todos.value[index].id; //todos의 value값에 index에 id값을 id에 담음
            try{
                await axios.delete('http://localhost:3000/todos/' + id)
                // 배열 삭제 splice 인덱스 번호중에서 하나 삭제
                //  todos.value.splice(index, 1)
                getTodos();//물어볼 부분
                console.log(getTodos)
            }catch(err){
                console.log(err);
                error.value="찾는 문장이 없습니다."
            }
        }


        // 체크박스를 체크하면 false값을 true로 바꾸기 위해서
        const toggleTodo = async (index) => {
            // console.log(index);
            error.value='';
            const id=todos.value[index].id; //내가 선택하는 친구를 보였다 안보였다 하기 위해서
            try{
                await axios.patch('http://localhost:3000/todos/' + id, {
                    completed: !todos.value[index].completed
                });
                todos.value[index].completed=!todos.value[index].completed; //true로 바꾸고 선 그어주기
            }catch(err){
                console.log(err)
                error.value="체크되지 않았습니다."
            }
        }
        // const filteredTodos = computed(()=>{
        //     if(searchText.value){
        //         return todos.value.filter(todo => {
        //             return todo.subject.includes(searchText.value); //같은 친구가 있나없나 보기위해
        //         })
        //     }
        //     return todos.value;
        // }) 
        return{
            todos,
            deleteTodo,
            addTodo,
            toggleTodo,
            /* filteredTodos, */
            searchText,
            error,
            getTodos,
            limit,
            numberofPages,
            currentPage,
            numberOfTodos
            // todoStyle
        }
    }
}
</script>

<style>
    *{margin: 0; padding: 0; box-sizing: border-box;}
    .container{widows: 100%; max-width: 1024px; margin: 0 auto; padding: 0 20px;}
    h2{text-align: center; color: skyblue; margin-bottom: 50px; margin-top: 50px;}
    .d-flex{display: flex;}
    form{ margin-bottom: 50px}
    .flex-grow-1{flex-grow: 1;}
     .flex-grow-1 input{width: 98%; padding: 10px 20px;}
    .btn{padding: 12px 30px; border: none; background: skyblue; color: #fff; transition: .3s;}
    .btn:hover{background: blue;}
    .card{margin: 10px 0;}
    .card-body{border: 1px solid #111; padding: 10px 20px; display: flex; justify-content: space-between; align-items: center;}
    .form-check-input{margin-right: 10px;}
    .todoStyle{color: gray; text-decoration: line-through;}
    .btnR{padding: 5px 20px; background: #e63a3a; color: white; border: none;}
    .form-control{width: 100%; border: 1px solid #ddd; margin-bottom: 10px; padding: 10px 20px;}

    .pagination{display: flex; list-style: none; justify-content: center;}
    .page-item{padding: 10px; margin: 0 3px; border: 1px solid #ddd;}
    .page-link{color: #666; text-decoration: none;}
    .active{background: #666; color: #fff;}
</style>