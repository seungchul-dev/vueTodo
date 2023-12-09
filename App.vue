<template>
	<main class="app">
		<section class="greeting" >
      <h1>
        What's up
        <input type="text" 
          placeholder="name here"
          v-model.trim.lazy="name">
      </h1>
    </section>

		<section class="create_todo">
      <h2>CREATE A TODO</h2>

      <form id="new-todo-form" @:submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input  
          class="todo_input"
          placeholder="할 일을 입력해 주세요"  
          v-model.trim.lazy="input_content"
        >

        <div class="options">          
          <label class="label">
            <input type="radio"     
              name="category" 
              id="category1"
              value="business"
              v-model="input_category">
              <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label class="label">
            <input type="radio" 
              name="category"
              id="category2"
              value="personal"
              v-model="input_category">
              <span class="bubble personal"></span>
            <div>Personal</div>            
          </label>
        </div>
        <input type="submit" value="Add Todo">        
      </form>
    </section>

		<section class="todo_list">
			<h3>TODO LIST</h3>

      <div class="list">
        <div :class="`todo_item ${todo.done &&  'done'}`" v-for="todo in todos_asc" :key="todo.createAt">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span 
              :class="`bubble  ${todo.category}`"  
              ></span>
          </label>
          <div class="todo_content">
            <input type="text" v-model="todo.content">           
          </div>
          <div class="actions">
            <button class="delete"
              @click="removeTodo(todo)"
            >Delete</button>
          </div>
        </div>
      </div>
		</section>

	</main>
</template>

<script setup>
	import { ref, watch, onMounted, computed } from 'vue';

  const name= ref('');
  const input_content= ref(null);
  const input_category= ref(null);
  const todos = ref([])


  //삭제 함수
  const removeTodo = (item) => {
    todos.value = todos.value.filter((aa)=>aa !== item)
  }


  //시간 순으로 정리(최신이 위에 오게)
  const todos_asc = computed(() =>todos.value.sort((a,b)=> b.createAt-a.createAt))

console.log("todos_asc ",todos_asc )
 //할일 입력값들을 받아오는 함수
const addTodo = () => {
	if(input_content.value.trim() === ''  ||  input_category.value === null){
    return
  } //입력값이 없거나 카테고리를 선택하지 않았을땐 코드블록 밖으로 빠져나감

  todos.value.push({
    content:input_content.value,
    category:input_category.value,
    done:false,
    createAt: new Date().getTime()
    //시간순으로 순서를 넣기 위해 UTC1970년 1월 1일  ~ 현재까지 몇초(ms)지났는지 알아옴
  })

    //입력 후 초기화
    input_content.value='';
    input_category.value=null
}

  //todos가 바뀌는 것 감지
  watch(todos, (newVal) => {
    //console.log('투두스 감지', newVal);
    localStorage.setItem('todos',JSON.stringify(newVal))

  },{deep:true})
  //deep - 속성의 프로퍼티나, 하위 뎁스도 갑지


//이름 입력하는 것을 감지하고 로컬스토리지에 저장, 업데이트
watch(name,(newVal)=>{
    localStorage.setItem('name',newVal)
  })

	//새로 열었을때 로컬스토리지에서 불러옴 (없을때는 비워놓음)
	onMounted(()=>{
    name.value = localStorage.getItem('name') || '';    
    todos.value = JSON.parse(localStorage.getItem('todos') || []);  
    console.log('투두스 새로 불러옴', todos.value);
  })

</script>
