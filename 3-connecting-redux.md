# **Connecting Redux to React**

Now that we have **Redux set up** with the **store and slice**, let’s connect Redux to our React app. This step ensures that our components can **access and update** the Redux state.

---

## **1️⃣ Wrap the App with the Redux Provider**
Redux needs to be available **globally** in our application. We do this by **wrapping our `<App />` component** with the `Provider` component from `react-redux`.

### **Step 1: Modify `index.js` to Include Redux Provider**
Open `src/index.js` and update it:

```js
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import { store } from "./redux/store";
import App from "./App";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

**📌 What This Does:**
- ✅ Provides access to the Redux store for the entire app.
- ✅ Ensures all components can use Redux state via hooks.

**📌 AI Debugging Prompt:** “What does <Provider> do in a Redux application? Why is it necessary?”

--- 

## 2️⃣ Using useSelector to Access State

Now, let’s create the to-do list component that will display tasks from Redux state.

### Step 2: Create TodoList.js

Inside `src/components/`, create a new file `TodoList.js` and add:

```JS
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { toggleComplete, deleteTodo } from "../redux/todoSlice";

const TodoList = () => {
  const todos = useSelector((state) => state.todos);
  const dispatch = useDispatch();

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => dispatch(toggleComplete(todo.id))}
          />
          {todo.text}
          <button onClick={() => dispatch(deleteTodo(todo.id))}>❌</button>
        </li>
      ))}
    </ul>
  );
};

export default TodoList;
```

**📌 What This Does:**
- ✅ useSelector retrieves todos from Redux state.
- ✅ useDispatch allows us to toggle completion and delete tasks.
- ✅ Uses checkboxes to mark tasks complete and buttons to delete tasks.

**📌 AI Debugging Prompt:** “Why do we use useSelector instead of passing props?”

---

## 3️⃣ Dispatching Actions with useDispatch

Now, let’s create the input form to add new tasks.

### Step 3: Create AddTodo.js

Inside `src/components/`, create a new file `AddTodo.js` and add:

```JS
import React, { useState } from "react";
import { useDispatch } from "react-redux";
import { addTodo } from "../redux/todoSlice";

const AddTodo = () => {
  const [text, setText] = useState("");
  const dispatch = useDispatch();

  const handleSubmit = (e) => {
    e.preventDefault();
    if (text.trim()) {
      dispatch(addTodo(text));
      setText("");
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <button type="submit">Add Todo</button>
    </form>
  );
};

export default AddTodo;
```

**📌 What This Does:**
- ✅ Uses local state (useState) to track input text.
- ✅ Calls dispatch(addTodo(text)) to add a new to-do when the form is submitted.
- ✅ Clears the input field after adding a task.

**📌 AI Debugging Prompt:** “What happens if you forget to call dispatch when using Redux?”

---

## 4️⃣ Update App.js to Include the Components

Now that we have our to-do list and add-to-do form, we need to import them into `App.js`.

### Step 4: Modify `App.js`

Open `src/App.js` and update it:

```JS
import React from "react";
import AddTodo from "./components/AddTodo";
import TodoList from "./components/TodoList";

function App() {
  return (
    <div>
      <h1>Redux To-Do List</h1>
      <AddTodo />
      <TodoList />
    </div>
  );
}

export default App;
```

**📌 What This Does:**
- ✅ Displays the app title.
- ✅ Includes <AddTodo /> to add tasks.
- ✅ Includes <TodoList /> to list tasks.

**📌 AI Debugging Prompt:** “What happens if useSelector returns undefined? How can you debug this?”

---

🚀 Now Redux is fully connected to React! Next, we will **[Enhancing the UI and add interactivity](4-enhancing-ui.md)**!
