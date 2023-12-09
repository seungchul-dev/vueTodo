<template>
  <div class="app">
    <section class="greeting">
      <h1>
        what's up
        <input type="text" placeholder="이름을 적어놓으세요" v-model.trim.lazy="name">
      </h1>
    </section>
    <section class="create_todo" @:submit.prevent="addTodo">
      <h2>CREATE</h2>
      <form id="new-todo-form"> 
        <h4>할일을 입력해주세용</h4>
        <input
        class="todo_input"
        placeholder="할 일을 입력해 주세요"
        v-model="input_content"
        >
        입력내용 - {{ input_content }}

        <div class="options">
          <label class="label">
            <input type="radio" name="catagory" id="catagory1" value="business" v-model="input_catagory">
            <span class="bubble"></span>
            <div>Business</div>
          </label>
         
          <label class="label">
          <input type="radio" name="catagory" id="catagory2" value="personal" v-model="input_catagory">
           <span class="bubble"></span>
            <div>Personal</div>
          </label>
         선택한 카테고리는 = {{ input_catagory }}
          <input type="submit" value="Add Todo">
        </div>

     

      </form>
    </section>
    <section class="todo_list">
      <h3>TODO LIST</h3>
      todos - {{ todos }}

    <div class="list">
      <div :class="`todo_item ${todo.done && 'done'}`" v-for="todo in todos">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
            <div class="todo_content">
              {{ todo.content }}
            </div>
            <div class="actions">
              <button class="delete" @click="removeTodo()">Delete</button>
            </div>
      </div>
    </div>
    </section>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';

  const name = ref("");
  const input_content = ref(null); //입력내용
  const input_catagory = ref(null);
  const todos = ref([])

//삭제함수
const removeTodo = (item) =>{
  todos.value = todos.value.filter((aa) => aa !== item)
}

  //시간 순으로 정리(최신이 위에 오게)
  const todos_asc = todos.value.sort((a,b) => b.createAt-a.createAt)

  //할일 입력바들을 받아오는 함수
  const addTodo = () => {
    if(input_content.value.trim === '' || input_catagory === null ){
      return
    } //입력밧이 없거나 카테고리를 선택하지 않았을땐 코드 블록 밖으로 빠져나감


    console.log("할일 입력값 받아오는 함수 실행")
    todos.value.push({
      content:input_content.value, 
      category:input_catagory.value,
      done:false,
      createAt: new Date().getTime()
    })

    //입력후 초기화
    input_content.value = '';
    input_catagory.value = null;
  }

  //todos가 바쒸는 것 감지
  watch(todos,(newVal)=>{
    console.log("todos 실행", newVal)
    localStorage.setItem('todos', JSON.stringify(newVal))
  },{deep:true})
  //deep - 속성의 프로퍼티나, 하위뎁스도 감지


  //이름입력을 인지 로컬스토러지에 저장
  watch(name, (newVal) =>{
    localStorage.setItem('name', newVal)
  })

  //새로 열였을때 불러오기
  onMounted(() => {
    name.value = localStorage.getItem('name') || '';
    todos.value = JSON.parse(localStorage.getItem('todos')|| []);
    console.log('투투스 새로 불러옴', todos.value);
  })
</script>

<style lang="scss" scoped>

</style>