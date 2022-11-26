
    
    <!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link
      href="https://fonts.googleapis.com/css?family=Poppins"
      rel="stylesheet"
    />
    <link rel="stylesheet" href="style.css" />
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/all.min.css"
      integrity="sha512-KfkfwYDsLkIlwQp6LFnl8zNdLGxu9YAA1QvwINks4PhcElQSvqcyVLLD9aMhXd13uQjoXtEKNosOWaZqXgel0g=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    />
    <title>ToDo List</title>
  </head>
  <body>
    <style>
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }

      body {
        background-image: linear-gradient(120deg, #f6d365, #d88771);
        color: white;
        font-family: "Poppins", sans-serif;
        min-height: 100vh;
        display: flex;
        flex-direction: column;
      }

      header {
        font-size: 1.5rem;
      }

      header,
      form {
        min-height: 20vh;
        display: flex;
        justify-content: center;
        align-items: center;
      }
      input{
        outline: none;
        padding: 1rem;
      }
      form input,
      form button {
        padding: 0.5rem;
        font-size: 2rem;
        border: none;
        /* background: white; */
        background-color: #65656550;
        color: #ffffff;
      }

      form button {
        color: #d88771;
        background: white;
        cursor: pointer;
        transition: all 0.3s ease;
      }

      form button:hover {
        background: #d88771;
        color: white;
      }

      .todo-container {
        display: flex;
        justify-content: center;
        align-items: center;
      }

      .todo-list {
        min-width: 30%;
        list-style: none;
      }

      .todo {
        margin: 0.5rem;
        /* background: white; */
        background-color: #65656550;
        color: #ffffff;
        font-size: 1.5rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
        transition: all 0.5s ease;
      }

      .todo li {
        flex: 1;
      }

      .trash-btn,
      .complete-btn {
        background: #ff6f47;
        color: white;
        border: none;
        padding: 1rem;
        cursor: pointer;
        font-size: 1rem;
      }

      .complete-btn {
        background: rgb(73, 204, 73);
      }

      .todo-item {
        padding: 0rem 0.5rem;
      }

      .fa-trash,
      .fa-check {
        pointer-events: none;
      }

      .completed {
        text-decoration: line-through;
        opacity: 0.5;
      }

      .fall {
        transform: translateY(8rem) rotateZ(20deg);
        opacity: 0;
      }

      select {
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
        outline: none;
        border: none;
      }

      .select {
        margin: 1rem;
        position: relative;
        overflow: hidden;
      }

      select {
        color: #ffffff;
        font-size: 1rem;
        width: 10rem;
        cursor: pointer;
        padding: 1rem;
        background-color: #65656550;
        display: flex;
        /* justify-content: center; */
        align-items: center;
        
      }

      .select::after {
        content: "\25BC";
        position: absolute;
        background: #ff6f47;
        top: 0;
        right: 0;
        padding: 1rem;
        pointer-events: none;
        transition: all 0.3s ease;
      }

      .select:hover::after {
        border-radius: 1rem;
        background: white;
        color: #ff6f47;
      }
    </style>
    <header>
      <h1>Todo List</h1>
    </header>
    <form>
      <input type="text" class="todo-input" />
      <button class="todo-button" type="submit">
        <i class="fas fa-plus-square"></i>
      </button>
      <div class="select">
        <select name="todos" class="filter-todo">
          <option value="all">All</option>
          <option value="completed">Completed</option>
          <option value="uncompleted">Uncompleted</option>
        </select>
      </div>
    </form>
    <div class="todo-container">
      <ul class="todo-list"></ul>
    </div>

    <script src="app.js"></script>
    <script>
      // Selectors
      const todoInput = document.querySelector(".todo-input");
      const todoButton = document.querySelector(".todo-button");
      const todoList = document.querySelector(".todo-list");
      const filterOption = document.querySelector(".filter-todo");

      // Event Listener
      document.addEventListener("DOMContentLoaded", getTodos);
      todoButton.addEventListener("click", addTodo);
      todoList.addEventListener("click", deleteCheck);
      filterOption.addEventListener("click", filterTodo);

      // Functions
      function addTodo(event) {
        event.preventDefault();

        // todo div
        const todoDiv = document.createElement("div");
        todoDiv.classList.add("todo");
        // create li
        const newTodo = document.createElement("li");
        newTodo.innerText = todoInput.value;
        newTodo.classList.add("todo-item");
        todoDiv.appendChild(newTodo);
        // add todo to localstorage
        saveLocalTodos(todoInput.value);
        // check mark button
        const completedButton = document.createElement("button");
        completedButton.innerHTML = '<i class="fas fa-check"></i>';
        completedButton.classList.add("complete-btn");
        todoDiv.appendChild(completedButton);
        // check trash button
        const trashButton = document.createElement("button");
        trashButton.innerHTML = '<i class="fas fa-trash"></i>';
        trashButton.classList.add("trash-btn");
        todoDiv.appendChild(trashButton);
        // append to list
        todoList.appendChild(todoDiv);
        // clear todo input value
        todoInput.value = "";
      }

      function deleteCheck(e) {
        const item = e.target;
        // delete todo
        if (item.classList[0] === "trash-btn") {
          const todo = item.parentElement;
          // animation
          todo.classList.add("fall");
          removeLocalTodos(todo);
          todo.addEventListener("transitionend", function () {
            todo.remove();
          });
        }
        // check mark
        if (item.classList[0] === "complete-btn") {
          const todo = item.parentElement;
          todo.classList.toggle("completed");
        }
      }

      function filterTodo(e) {
        const todos = todoList.childNodes;
        todos.forEach(function (todo) {
          switch (e.target.value) {
            case "all":
              todo.style.display = "flex";
              break;
            case "completed":
              if (todo.classList.contains("completed")) {
                todo.style.display = "flex";
              } else {
                todo.style.display = "none";
              }
              break;
            case "uncompleted":
              if (!todo.classList.contains("completed")) {
                todo.style.display = "flex";
              } else {
                todo.style.display = "none";
              }
              break;
          }
        });
      }

      function saveLocalTodos(todo) {
        // check if something exists
        let todos;
        if (localStorage.getItem("todos") === null) {
          todos = [];
        } else {
          todos = JSON.parse(localStorage.getItem("todos"));
        }

        todos.push(todo);
        localStorage.setItem("todos", JSON.stringify(todos));
      }

      function getTodos() {
        let todos;
        if (localStorage.getItem("todos") === null) {
          todos = [];
        } else {
          todos = JSON.parse(localStorage.getItem("todos"));
        }
        todos.forEach(function (todo) {
          // todo div
          const todoDiv = document.createElement("div");
          todoDiv.classList.add("todo");
          // create li
          const newTodo = document.createElement("li");
          newTodo.innerText = todo;
          newTodo.classList.add("todo-item");
          todoDiv.appendChild(newTodo);
          // check mark button
          const completedButton = document.createElement("button");
          completedButton.innerHTML = '<i class="fas fa-check"></i>';
          completedButton.classList.add("complete-btn");
          todoDiv.appendChild(completedButton);
          // check trash button
          const trashButton = document.createElement("button");
          trashButton.innerHTML = '<i class="fas fa-trash"></i>';
          trashButton.classList.add("trash-btn");
          todoDiv.appendChild(trashButton);
          // append to list
          todoList.appendChild(todoDiv);
        });
      }

      function removeLocalTodos(todo) {
        let todos;
        if (localStorage.getItem("todos") === null) {
          todos = [];
        } else {
          todos = JSON.parse(localStorage.getItem("todos"));
        }
        const todoIndex = todo.children[0].innerText;
        todos.splice(todos.indexOf(todoIndex), 1);
        localStorage.setItem("todos", JSON.stringify(todos));
      }
    </script>
  </body>
</html>

    
