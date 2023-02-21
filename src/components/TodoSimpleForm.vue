<template>
    <form  @submit.prevent="onSubmit">
        <div class="d-flex">
            <div class="flex-grow-1">
                <input type="text" placeholder="오늘 할 일을 작성해주세요." v-model="todo">
            </div>
            <div class="b-btn">
                <button class="btn" type="submit">ADD</button>
            </div>
        </div>
        <div v-show="hasError" style="color: tomato">
            해야 할 일이 없습니다. 새로운 일을 작성해주세요!
        </div>
    </form>
</template>

<script>
import {ref} from 'vue';
export default{
    emits: ['add-todo'],
    setup(props, context){
        const todo = ref('');
        const hasError=ref(false);
        const onSubmit = () => {
            if(todo.value===""){
                hasError.value=true;
            }else{
                context.emit('add-todo', {
                    id: Date.now(),
                    subject: todo.value,
                    completed:false
                    }
                )
                hasError.value=false;
                todo.value=""
            }
            
        }
        return{
            todo,hasError,onSubmit
        }
    }
}
</script>

<style>
</style>