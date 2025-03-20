# **Project Setup** 🚀

Now that we understand **why we’re using Redux**, let’s set up our **React + Redux To-Do List project**.

---

## **1️⃣ Install Required Dependencies**

First, make sure you have **Node.js installed**. If not, download it from [Node.js Official Website](https://nodejs.org/).

Now, create a new React project and install the necessary dependencies.

### **Step 1: Create a New React App**

Open a terminal and run the following commands:

```sh
npx create-react-app todo-list
cd todo-list
npm start
```

**📌 What This Does:**
- ✅ Creates a new React project named todo-list.
- ✅ Moves into the project folder.
- ✅ Starts the development server, opening the app in your browser.

**📌 AI Debugging Prompt:** “How do I check if my React app is set up correctly?”

---

### Step 2: Install Redux Toolkit & React-Redux

Now, install Redux Toolkit and React-Redux:

```sh
npm install @reduxjs/toolkit react-redux
```

**📌 What This Does:**
- ✅ @reduxjs/toolkit – Simplifies Redux state management.
- ✅ react-redux – Provides the necessary tools to connect Redux with React.

**📌 AI Debugging Prompt:** “How can I verify that Redux Toolkit is installed correctly?”

---

## 2️⃣ Set Up the Redux Store

Now, let’s create the Redux store and configure it properly.

### Step 3: Create the Redux Folder Structure

Inside `src/`, create a new folder called `redux/` to store all Redux-related files.

**📌 Your `src/` folder should now look like this:**

```
src/
  ├── components/   
  ├── redux/        <-- 📂 New folder for Redux
  ├── App.js
  ├── index.js
  ├── ...
```

---

### Step 4: Configure the Redux Store

Inside the `redux/` folder, create a new file called `store.js` and add the following code:

```JS
import { configureStore } from "@reduxjs/toolkit";
import todoReducer from "./todoSlice";

export const store = configureStore({
  reducer: {
    todos: todoReducer,
  },
});
```

**📌 Explanation:**
- ✅ configureStore() – Creates a Redux store with minimal setup.
- ✅ todoReducer – Handles the to-do list state (we’ll create this next).

**📌 AI Debugging Prompt:** “Why do we pass an object to `configureStore` instead of manually combining reducers?”

---

## 3️⃣ Set Up the To-Do Slice

Now, we need to define the to-do state and actions (adding, completing, and deleting tasks).

### Step 5: Create the To-Do Slice

Inside `redux/`, create a new file called `todoSlice.js` and add:

```JS
import { createSlice } from "@reduxjs/toolkit";

const initialState = [
  { id: 1, text: "Learn Redux", completed: false },
];

const todoSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    addTodo: (state, action) => {
      state.push({ id: Date.now(), text: action.payload, completed: false });
    },
    toggleComplete: (state, action) => {
      const todo = state.find(todo => todo.id === action.payload);
      if (todo) todo.completed = !todo.completed;
    },
    deleteTodo: (state, action) => {
      return state.filter(todo => todo.id !== action.payload);
    },
  },
});

export const { addTodo, toggleComplete, deleteTodo } = todoSlice.actions;
export default todoSlice.reducer;
```

**📌 What This Does:**
- ✅ Defines a Redux slice called "todos".
- ✅ Stores tasks in an array with { id, text, completed }.
- ✅ Adds reducers for:
  -	`addTodo` – Adds a new task to the list.
  -	`toggleComplete` – Marks a task as complete/incomplete.
  -	`deleteTodo` – Removes a task from the list.

**📌 AI Debugging Prompt:** “What happens if I try to modify state directly inside a reducer?”

---

Now that your project is setup, move on to *[Connecting Redux to React](3-connecting-redux.md)**!
