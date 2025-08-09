<template>
<div class="card">
    <h2>註冊</h2>
    <input v-model="registerFields.email" type="email" placeholder="Email" />
    <input v-model="registerFields.password" type="password" placeholder="Password" />
    <input v-model="registerFields.nickname" type="text" placeholder="Nickname" />
    <!-- {{registerFields}} -->
    <button @click="register" type="button">註冊</button>
    {{registerRes}}
</div>
<div class="card">
    <h2>登入</h2>
    <input v-model="signInFields.email" type="email" placeholder="Email" />
    <input v-model="signInFields.password" type="password" placeholder="Password" />
    <!-- {{signInFields}} -->
    <button @click="signIn" type="button">登入</button>
    <br>
    signintoken:{{ signInRes }}
</div>
<div class="card">
    <h2>驗證</h2>
    <input type="text" placeholder="token" v-model="auttoken"/>
    <button type="button" @click="checkout">驗證</button>
    <h3>{{stateText}}</h3>
</div> 
<div class="card">
    <h2>Todolist</h2>
    <template v-if="token">
        <input type="text" v-model="newTodo"/>
        <button type="button" @click="addTodo">+</button>
        <ul v-for="todo in todoList" :key="todo.id" >
            <li>
                {{ todo.content }} |{{ todo.completed }}|
                <input type="text" v-model="newContent"/>
                <button type="button" @click="contentchange(todo.id)">修改文字</button>
                <button type="button" @click="delTodo(todo.id)">刪除</button>
                <button type="button" @click="changeTodoStatus(todo.id)">修改狀態</button>
            </li>
        </ul>
    </template>
    <p v-else>請先登入以使用待辦事項功能</p>
</div>

</template>


<script setup>
    import { onMounted,ref } from 'vue';
    import axios from 'axios';
    const api='https://todolist-api.hexschool.io/';  
    //註冊
    const registerFields = ref({
        email: '',
        password: '',
        nickname: ''
    });
    const registerRes=ref('');
    const register = async()=>{
        try{
            const res= await axios.post(`${api}users/sign_up`,registerFields.value);
            alert("註冊成功");
            // console.log(res);
            registerRes.value = res.data.uid;
        }
        catch(e){
            console.log(e);
            alert("註冊失敗，請檢查輸入的資料是否正確");
        }   
    }
    //登入
    const signInFields = ref({
        email: '',
        password: ''
    });
    const token = ref('');
    const signInRes = ref('');
    const signIn = async()=>{
        try{
            const res= await axios.post(`${api}users/sign_in`,signInFields.value);
            alert("登入成功");
            // console.log(res);
            signInRes.value = res.data.token;
            document.cookie = `regisertoken=${res.data.token}; path=/;`;
            token.value = res.data.token;
        }
        catch(e){
            console.log(e);
            alert("登入失敗");
        } 
    }  
    // 使用者驗證
    const stateText=ref('');
    const auttoken = ref('');
    
    const checkout = async()=>{
        try{
            const res = await axios.get(`${api}users/checkout`, {
                headers: {
                    Authorization: auttoken.value
                }         
            });
            stateText.value = `token：${auttoken.value}驗證成功`
            
        } catch(e) {
            console.log(e);
            alert("驗證失敗，請檢查token是否正確");
            stateText.value = `error:${e}驗證失敗`
        }
        
    }

    //todolist
    const todoList = ref([]);
    const newTodo = ref('');
    const TodoSta = ref('未完成');
    const newContent = ref('');
    const addTodo = ()=>{
        try{
            // console.log("新增代辦事項");
            todoList.value.push({
            id: new Date().getTime(),
            content: newTodo.value,
            completed: TodoSta.value
        });
        }
        catch(e){
            console.log(e);
        }
        
    }
    const delTodo =(id)=>{
        try{
            // console.log("刪除代辦事項", id);
            const index = todoList.value.findIndex(todo =>todo.id ===id);
            todoList.value.splice(index, 1);
        }catch(e){
            console.log(e);
        }
       
    }
    const changeTodoStatus = (id) =>{
        try{
            const index = todoList.value.findIndex(todo =>todo.id ===id);
            if(todoList.value[index].completed === '未完成'){
            todoList.value[index].completed = '已完成';
            } else {
            todoList.value[index].completed = '未完成';
        }
        }catch(e){
            console.log(e);
        }
      
    }
    const contentchange = (id) =>{
        try{
            const index=todoList.value.findIndex(todo => todo.id === id);
            todoList.value[index].content = newContent.value;
        }catch(e){
            console.log(e);
        }
        
    }

</script>

<style scoped>
    .div {
        margin: 20px;
        font-family: 'Noto Sans TC',Arial;
    }
    .card {
        border: 2px solid #ccc;
        padding: 20px;
        margin-bottom: 20px;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        width: 600px;
    }
    .input {
        display: flex;
        border-radius: 4px;
        border: 1100px solid #831616;
        padding: 200px;
    }
    .li{
        display: flex;
        padding: 10px 0;
        border-bottom: 1px solid #474343;
    }
</style>