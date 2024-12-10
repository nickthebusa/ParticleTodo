<script>
import TODOItem from './TODOItem.vue'

export default {
  components: {
    TODOItem
  },
  data() {
    return {
      todoItems: [],
      textInput: '',
      imgStr: '',
      todoTitle: `
▄▄▄█████▓ ▒█████  ▓█████▄  ▒█████  
▓  ██▒ ▓▒▒██▒  ██▒▒██▀ ██▌▒██▒  ██▒
▒ ▓██░ ▒░▒██░  ██▒░██   █▌▒██░  ██▒
░ ▓██▓ ░ ▒██   ██░░▓█▄   ▌▒██   ██░
  ▒██▒ ░ ░ ████▓▒░░▒████▓ ░ ████▓▒░
  ▒ ░░   ░ ▒░▒░▒░  ▒▒▓  ▒ ░ ▒░▒░▒░ 
    ░      ░ ▒ ▒░  ░ ▒  ▒   ░ ▒ ▒░ 
  ░      ░ ░ ░ ▒   ░ ░  ░ ░ ░ ░ ▒  
             ░ ░     ░        ░ ░  
                   ░               

      `
    }
  },
  methods: {
    addItem() {
      if (this.textInput.trim() !== "") {
        const date = new Date();
        const newItem = {
          title: this.textInput.trim(),
          date: date.toDateString(),
          img: this.imgStr.trim(),
        };
        this.todoItems.push(newItem);
        localStorage.setItem("todo-items", JSON.stringify(this.todoItems));
        this.textInput = '';
        this.imgStr = '';
      }
    },
    removeItem(idx) {
      this.todoItems.splice(idx, 1);
      localStorage.setItem("todo-items", JSON.stringify(this.todoItems));
    }
  },

  mounted() {
    this.$refs.addTodoInput.focus();
    const storedItems = JSON.parse(localStorage.getItem("todo-items"));
    if (storedItems) {
      this.todoItems = [...storedItems];
    }
  }
}
</script>


<template>

  <div class="TODO">
    <div class="title">
      <pre>{{ todoTitle }}</pre>
    </div>

    <div class="blur-gradient"></div>

    <div class="input">
      <input type="text" placeholder="add a TODO" ref="addTodoInput" v-model="textInput" @keyup.enter="addItem">
      <input type="text" placeholder="img url" class="img-txt" v-model="imgStr">
      <button @click="addItem">ADD</button>
    </div>

    <div class="list">
      <TODOItem v-for="(value, index) in todoItems" :key="index" :index="index" :value="value"
        @removeItem="removeItem" />
    </div>
  </div>

</template>

<style scoped>
.TODO {
  width: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
}

.TODO .title {
  margin: -1rem 0 0 0;
  display: flex;
  justify-content: center;
}

.TODO .title pre {
  margin: 0;
  font-size: 0.9rem;
  letter-spacing: 0;
}

.TODO .input {
  display: flex;
  flex-wrap: nowrap;
  justify-content: center;
  margin: -7.5rem 5px;
}

.TODO .input input,
.TODO .input button {
  font-size: 1rem;
  font-family: monospace;
  padding: 0.5rem 0.8rem;
  border-width: 2px;
  border-style: inset;
  border-color: light-dark(rgb(118, 118, 118), rgb(133, 133, 133));
  border-image: initial;
  border-radius: 0.5rem;
  margin: 0;
  white-space: nowrap;
}

.TODO .input input {
  width: 100%;
  max-width: 25rem;
  min-width: 1rem;
}

.TODO .input .img-txt {
  width: 5rem;
}

.TODO .input button {
  margin: 0 0 0 1rem;
  cursor: pointer;
}

.TODO .list {
  display: flex;
  flex-direction: column;
  margin: 10rem 0 0 0;
  width: 100%;
  box-sizing: border-box;
}

.blur-gradient {
  position: absolute;
  width: 100%;
  z-index: -1;
  height: 300px;
  top: 0;
  background: gray;
  /* Adjust as needed */
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  /* For Safari */
  mask-image: linear-gradient(to bottom, black, transparent);
  -webkit-mask-image: linear-gradient(to bottom, black, transparent);
  /* For Safari */
  pointer-events: auto;
}

.blur-gradient::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

@media (max-width: 365px) {

  .TODO .input input,
  .TODO .input button {
    font-size: 0.8rem;
    padding: 0.4rem 0.4rem;
  }
}
</style>
