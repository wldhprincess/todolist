
<template>
    <div class="container">
        <h2>ToDo-List</h2>
        <button class="btn btnB" @click="moveToCreatePage" >Create ToDo</button>
        <input type="text" placeholder="검색" class="form-control" v-model="serchText">
        <!-- <TodoSimpleForm @add-todo="addTodo"/> -->
        <div v-if="!todos.length">
            오늘의 할 일이 없습니다.
        </div>
        <TodoList :todos="todos" @delete-todo="deleteTodo"  @toggle-todo="toggleTodo"/>
        <br>
        <nav class="nav">
            <ul class="pagination">
                <li class="page-item" v-if="currentPage!==1"><a href="#" class="page-link" @click="getTodos(currentPage-1)"><i class="fa-solid fa-chevron-left"></i></a></li>
                <li class="page-item" v-for="page in numberOfPage" :key="page" :class="currentPage === page ? 'active' : ''"><a href="#" :class="currentPage === page ? 'active' : ''" class="page-link" @click="getTodos(page)">{{ page }}</a></li>
                <li class="page-item" v-if="numberOfPage!==currentPage"><a href="#" class="page-link" @click="getTodos(currentPage+1)"><i class="fa-solid fa-chevron-right"></i></a></li>
            </ul>
        </nav>
    </div>
</template> <!-- 뷰영역 -->
<script>
import {computed, ref, watch} from 'vue';
/* import TodoSimpleForm from "@/components/TodoSimpleForm.vue"; */
import TodoList from "@/components/TodoList.vue";
import axios from 'axios';
import {useRouter} from 'vue-router';

export default {
    components: {
        /* TodoSimpleForm, */
        TodoList
    },
    setup(){
        const router = useRouter();
        const todos = ref([]);
        const serchText=ref('');
        const error = ref('');
        const limit=5;
        const numberOfTodos=ref(0);
        const currentPage=ref(1);
        const numberOfPage=computed(()=>{
            return Math.ceil(numberOfTodos.value/limit);
        });
        const getTodos = async (page = currentPage.value) => {
            currentPage.value=page;
            try{
                const res= await axios.get(`http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${serchText.value}&_page=${page}&_limit=${limit}`); //상태값(ref)는 value를 뒤에 붙여줘야함
                //console.log(res.headers);
                numberOfTodos.value=res.headers['x-total-count'];
                todos.value=res.data
            }catch(err){
                console.log(err);
                err.value="찾는 문장이 없습니다."
            }
        };
        getTodos();

        const moveToCreatePage = () => {
            router.push({
                name:'TodoCreate',
            })
        }

        const addTodo = async(todo)=>{
            error.value='';
            try{await axios.post('http://localhost:3000/todos', {
                    subject: todo.subject,
                    completed: todo.completed
                });
                getTodos();
            }catch(err){
                console.log(err);
                err.value="잘못된 입력방식입니다."
            }
        }
        watch(serchText,(/* current,prev */)=>{
            /* console.log(current,prev) */
            getTodos(1);
        })
        /* const addTodo = (todo)=>{
            error.value='';
            axios.post('http://localhost:3000/todos',{
                subject: todo.subject,
                completed: todo.completed
            }).then((res)=>{
                todos.value.push(res.data);

            }).catch((error)=>{
                console.log(error);
                error.value="잘못된 입력방식입니다."
            })
        } */
        /* const todoStyle={
            color:'grey',
            textDecoration:'line-through'
        } 스타일 바인딩 ver*/

        
        const deleteTodo = async(index) =>{
            error.value='';
            const id =todos.value[index].id;

            try{await axios.delete('http://localhost:3000/todos/'+id);
               /*  todos.value.splice(index, 1); */
               getTodos();
            }catch(err){
                console.log(err);
                err.value="찾는 문장이 없습니다."
            }  
        }

        const toggleTodo= async(index,checked)=>{
            error.value='';
            const id =todos.value[index].id;

            try{await axios.patch('http://localhost:3000/todos/'+id, {
                completed: checked
            });
                todos.value[index].completed=checked
            }catch(err){
                console.log(err);
                err.value="찾는 문장이 없습니다."
            }  

            
        }

        /* const filteredTodos = computed(()=>{
            if(serchText.value){
                return todos.value.filter(todo => {
                    return todo.subject.includes(serchText.value);
                })
            }
            return todos.value;
        }) */
        return{
            todos /* todoStyle 스타일 바인딩 ver*/, deleteTodo, addTodo, toggleTodo, serchText, /* filteredTodos, */ getTodos, numberOfPage, currentPage, moveToCreatePage
        }
    }
}
</script> <!-- js영역 -->

<style>
    *{margin: 0; padding: 0; box-sizing: border-box;}
    .container{width: 100%; max-width: 1024px; margin: 0 auto; padding: 0 20px;}
    h2{text-align: center; color: mediumaquamarine; margin-bottom: 50px; margin-top: 50px; font-size: 2ewm;}
    .d-flex{display: flex;}
    .flex-grow-1{flex-grow: 1;}
    .flex-grow-1 input{width: 98%; padding: 10px 20px;}
    .btn{padding: 12px 30px; border: none; background: #7fd7ba; color: #fff; border-radius: 5px; transition: .3s;}
    .btn:hover{background: mediumaquamarine;}
    form{margin-bottom: 50px;}
    .card{margin: 10px 0; }
    .card-body{background: #dff6ee; padding: 10px 20px; border-radius: 5px; display: flex; justify-content: space-between; align-items: center;}
    .form-check-input{margin-right: 10px;}
    .todoStyle{color: grey; text-decoration: line-through;}
    .btnR{padding: 5px 20px; background: #bbb; color: #fff; border: none; border-radius: 5px; transition: .3s;}
    .btnR:hover{background: #a6a6a6;}
    .form-control{width: 100%; margin-bottom: 10px; padding: 10px 20px; background: #f5f5f5; border: 0; border-radius: 5px;}
    .pagination{list-style: none; display: flex; justify-content: center;}
    .page-item{padding: 10px; margin: 0 5px; border: 1px solid #ddd;}
    .page-link{color: #666; text-decoration: none;}
    .active{background: #666; color: #fff;}
    .btnB{margin-bottom: 10px;}


</style> <!-- css영역 -->