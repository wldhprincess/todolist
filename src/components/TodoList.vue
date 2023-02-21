<template>
    <div>
        <div v-for="(todo, index) in todos" :key="todo.id" class="card"> <!-- v-for사용시 유니크한 key값이 필요 -->
            <div class="card-body" @click="moveToPage(todo.id)">
                <div class="form-check">
                    <input type="checkbox" class="form-check-input" :checked="todo.completed" @change="toggleTodo(index, $event)" @click.stop> <!-- for문 안에 id 설정시 반복이 안되기에 정확하게 input을 클릭할 수 있도록 만들어야함 -->
                    <label class="form-check-label" :class="{todoStyle:todo.completed}">
                        {{ todo.subject }}
                    </label>
                    <!-- <label class="form-check-label" :style="todo.completed ? todoStyle : {}">
                        {{ todo.subject }}
                    </label> 스타일 바인딩 ver-->
                </div>
                <div>
                    <button class="btnR" @click.stop="deleteTodo(index)">삭제</button>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import router from '@/router';
import {useRouter} from 'vue-router';

export default{
    props: {
        todos: {
            type:Array,
            require:true
        }
    },
    emits:['toggle-todo', 'delete-todo'],
    setup(props, context){
        const router=useRouter();
        const toggleTodo = (index, event )=> {
            context.emit('toggle-todo', index, event.target.checked)
        }
        const deleteTodo = (index) => {
            context.emit('delete-todo', index);
        }

        const moveToPage = (todoId) =>{
            //console.log(todoId)
            //router.push('/todos/' + todoId)
            router.push({
                name: 'Todo',
                params: {
                    id: todoId
                }
            })
        }

        return{
            deleteTodo,
            toggleTodo,
            moveToPage
        }
    }
}
</script>