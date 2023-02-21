<template>
    <div class="container">
        <div v-if="loading">
            Loading...
        </div>
        <form v-else @submit.prevent="onSave">
            <div class="row1">
                <div>
                    <div class="form-group">
                        <label>Todo Subject</label>
                        <input type="text" class="form-control" v-model="todo.subject">
                        <div v-if="subjectError">{{ subjectError }}</div>
                    </div>
                </div>
                <div v-if="aditing">
                    <div class="form-group">
                        <label>Status</label>
                        <div>
                            <button @click="toggleTodoStatus" type="button" class="btn" :class="todo.completed ? 'btnGG':'btnRR'">{{todo.completed ? '완료':'미완료'}}</button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="bodyWrap">
                <div class="form-group">
                    <label>Body</label>
                    <textarea v-model="todo.body" cols="30" rows="10"></textarea>
                </div>
            </div>
            <button class="btnmt" type="submit" :disabled="!todoUpdated">{{ editing ? '저장' : '만들기' }}</button>
            <button class="btnW btnl" @click="moveToTodoListPage">취소</button>
        </form>
    </div>
</template>

<script>
    import {useRoute, useRouter} from 'vue-router';
    import axios from 'axios';
    import { ref, computed} from 'vue';
    import _ from 'lodash';
    export default {
        props:{
            editing: {
                type: Boolean,
                default: false
            }
        },
        setup(props) {
            const route=useRoute();
            const router=useRouter();
            const subjectError = ref('');
            const todo=ref({
                subject:'',
                completed:false,
                body:''
            });
            const loading= ref(true);
            const originalTodo=ref(null);
            const todoId=route.params.id

            //console.log(route.params.id)
            const getTodo = async () =>{
                const res = await axios.get(`http://localhost:3000/todos/${todoId}`);
                todo.value=res.data;
                loading.value=false;
                originalTodo.value = {...res.data} 
            }
           

            const toggleTodoStatus = () =>{
                todo.value.completed = !todo.value.completed;
            }
            const moveToTodoListPage =() =>{
                router.push({
                    name:'Todos'
                })
            };
            if(props.editing){
                getTodo();
            }
            

            const onSave = async () => {
                subjectError.value='';
                if(!todo.value.subject){
                    subjectError.value='내용을 작성해주세요!'
                    return;
                }
                try{
                    let res;
                    const data={
                        subject:todo.value.subject,
                        completed:todo.value.completed,
                        body:todo.value.body,
                    }
                    if(props.editing){
                        res = await axios.put(`http://localhost:3000/todos/${todoId}`,data);
                    } else {
                         res = await axios.post(`http://localhost:3000/todos`, data);
                         todo.value.subject=""; 
                         todo.value.body=""; 
                   
                };
                    originalTodo.value = {...res.data}    
                }catch(error){
                    console.log(error);
                }
               
            }
            const todoUpdated = computed(() => {
                return !_.isEqual(todo.value, originalTodo.value)
            }) 
            return {
                todo,
                toggleTodoStatus,
                moveToTodoListPage,
                onSave,
                todoUpdated,
                subjectError
                /* loading, */
            }
        }
    }

</script>
<style>
    h1{margin: 30px 0; text-align: center;}
    .form-group{
        display: flex;
        flex-direction: column;
        margin-bottom: 10px;
    }
    .form-group label{font-weight: bold; margin-bottom: 5px;}
    .form-group .form-control{padding: 13.5px 20px; outline: none;}
    .row1{display: flex; justify-content: space-between;}
    .row1>div{flex-basis: 49%;}
    .Sz{padding: 9px 20px;}
    .btnW{background: tomato;; color: #fff; padding: 12px 30px; border: none; border-radius: 5px;}
    .btnL{margin-left: 10px;}
    
</style>
<style scoped>
.row1{margin-bottom: 10px;}
.btnmt{padding: 12px 30px; border: none; margin-right: 10px; border-radius: 5px;}
</style>
