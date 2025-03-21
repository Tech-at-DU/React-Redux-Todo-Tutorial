# **Adding Task Sorting**

Now that we can **filter tasks**, let's enhance the app further by **adding sorting options**. Sorting allows users to:
- ✅ **Sort tasks by newest/oldest** (based on creation time).  
- ✅ **Sort by completed status** (completed tasks first).  
- ✅ **Persist sorting preferences** using Redux.  

---

## **1️⃣ Define Sorting Options in Redux**
We need to **store the sorting preference** in Redux state.

### **Step 1: Create `sortingSlice.js`**
Inside `redux/`, create a new file **`sortingSlice.js`** and add:

```js
import { createSlice } from "@reduxjs/toolkit";

export const SORTING = {
  NEWEST_FIRST: "newest",
  OLDEST_FIRST: "oldest",
  COMPLETED_FIRST: "completed",
};

const sortingSlice = createSlice({
  name: "sorting",
  initialState: SORTING.NEWEST_FIRST,
  reducers: {
    setSorting: (state, action) => action.payload,
  },
});

export const { setSorting } = sortingSlice.actions;
export default sortingSlice.reducer;
```

📌 **What This Does:**  
- ✅ Defines sorting options (`NEWEST_FIRST`, `OLDEST_FIRST`, `COMPLETED_FIRST`).  
- ✅ Stores **current sorting preference** in Redux state.  
- ✅ Provides **`setSorting`** action to update sorting order.  

📌 **AI Debugging Prompt:** *"Why do we store sorting preferences in Redux instead of local component state?"*

---

## **2️⃣ Update Store to Include Sorting**
Now, we need to **add `sortingSlice` to the Redux store**.

### **Step 2: Modify `store.js` to Include Sorting**
Open `redux/store.js` and update it:

```js
import { configureStore } from "@reduxjs/toolkit";
import todoReducer from "./todoSlice";
import filtersReducer from "./filtersSlice";
import sortingReducer from "./sortingSlice"; // 1️⃣
import { loadState, saveState } from "./localStorage";

const preloadedState = loadState() || { todos: [] };

export const store = configureStore({
  reducer: {
    todos: todoReducer,
    filters: filtersReducer,
    sorting: sortingReducer, // 2️⃣
  },
  preloadedState, 
});

store.subscribe(() => {
  saveState({
    todos: store.getState().todos,
    filters: store.getState().filters,
    sorting: store.getState().sorting // 3️⃣
  });
});
```

📌 **What This Does:**  
- ✅ Adds `sortingReducer` to Redux store.  
- ✅ Saves **sorting preferences** in LocalStorage.  

📌 **AI Prompt:** *"Why do we include sorting in the persisted state?"*

📌 **AI Prompt:** *"What does the application state for this app look like? <include the code above>"*

---

## **3️⃣ Create Sorting Controls**
Now, we need a **dropdown menu** to let users choose a sorting order.

### **Step 3: Create `SortingControls.js`**
Inside `src/components/`, create a new file **`SortingControls.js`** and add:

```js
import React from "react";
import { useDispatch, useSelector } from "react-redux";
import { setSorting, SORTING } from "../redux/sortingSlice";

const SortingControls = () => {
  const dispatch = useDispatch();
  const currentSorting = useSelector((state) => state.sorting);

  return (
    <select value={currentSorting} onChange={(e) => dispatch(setSorting(e.target.value))}>
      <option value={SORTING.NEWEST_FIRST}>Newest First</option>
      <option value={SORTING.OLDEST_FIRST}>Oldest First</option>
      <option value={SORTING.COMPLETED_FIRST}>Completed First</option>
    </select>
  );
};

export default SortingControls;
```

📌 **What This Does:**  
- ✅ Displays a dropdown with sorting options.  
- ✅ Uses `useDispatch` to update Redux state when sorting changes.  
- ✅ Uses `useSelector` to track **current sorting order** in Redux state.  

**☝️Note!** You won't see any change in the app yet. There may even be an error! You will take care of this in the next few steps. 

---

## **4️⃣ Apply Sorting to Todo List**
Now, let’s modify `TodoList.js` to **sort tasks** before rendering.

### **Step 4: Modify `TodoList.js` to Apply Sorting**
Open `TodoList.js` and update it:

```js
import React, { useState } from "react";
import { useSelector, useDispatch } from "react-redux";
import { toggleComplete, deleteTodo } from "../redux/todoSlice";
import { FILTERS } from "../redux/filtersSlice"; 
import { SORTING } from "../redux/sortingSlice"; // 1️⃣

const TodoList = () => {
  const todos = useSelector((state) => state.todos); 
  const filter = useSelector((state) => state.filters);
  const sorting = useSelector((state) => state.sorting); // 2️⃣
  const dispatch = useDispatch();
  const [removingId, setRemovingId] = useState(null);

  const handleDelete = (id) => {
    setRemovingId(id);
    setTimeout(() => {
      dispatch(deleteTodo(id));
      setRemovingId(null);
    }, 300);
  };

  const filteredTodos = todos.filter((todo) => {
    if (filter === FILTERS.ACTIVE) return !todo.completed;
    if (filter === FILTERS.COMPLETED) return todo.completed;
    return true;
  });

  // 3️⃣
  const sortedTodos = [...filteredTodos].sort((a, b) => {
    if (sorting === SORTING.NEWEST_FIRST) return b.id - a.id;
    if (sorting === SORTING.OLDEST_FIRST) return a.id - b.id;
    if (sorting === SORTING.COMPLETED_FIRST) return b.completed - a.completed;
    return 0;
  });

  return (
    <ul>
      {sortedTodos.map((todo) => ( // 4️⃣ Just update this line!
        <li
          key={todo.id}
          className={`${todo.completed ? "completed" : ""} ${removingId === todo.id ? "removed" : ""}`}
        >
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => dispatch(toggleComplete(todo.id))}
          />
          {todo.text}
          <button onClick={() => handleDelete(todo.id)}>❌</button>
        </li>
      ))}
    </ul>
  );
};

export default TodoList;
```

📌 **What This Does:**  
- ✅ Filters tasks **first**, then sorts them based on selected option.  
- ✅ Ensures **tasks update instantly** when sorting changes.  

**📌 AI Prompt:** "What is the interaction between todos, filteredTodos, and sortedTodos? <inlcude the code block>"

---

## **5️⃣ Update `App.js` to Include Sorting**
Finally, let’s import `SortingControls.js` into `App.js`.

### **Step 5: Modify `App.js` to Include Sorting**
Open `App.js` and update it:

```js
import React from "react";
import AddTodo from "./components/AddTodo";
import TodoList from "./components/TodoList";
import FilterControls from "./components/FilterControls";
import SortingControls from "./components/SortingControls"; // 1️⃣

import './App.css'

function App() {
  return (
    <div>
      <h1>Redux To-Do List</h1>
      <AddTodo />
      <FilterControls />
      <SortingControls /> {/* 2️⃣ */}
      <TodoList />
    </div>
  );
}

export default App;
```

**🐞Note!** You may have an error at this stage. We've updated the shape of the store. Previously the store did not include a sorting key, and that is missing then the data is loaded from localStarage. 

**🔧Fix** this by deleting localStorage. Open your browsers dev tools and find "Local Storage - Localhost". It should shows "todos". You should be able to select it and press delete to remove it. 

**🧪Test** your work. 
- Add a couple new todo items. Do they appear in the list? 
- Delete a todo item. Does it disappear from the list? 
- Mark a todo item as completed. Does the style change? It should show gray with a line through the text. 
- Filter todos. 
  - Select "Active" from the menu. Only active todos should be visible. 
  - Select "Completed". Only the completed todo should be visible. 
  - Select "All". All todos should be visible. 
- Sort todos. 
  - Select "Completed First", the completed todo should appear on top. 
  - Select "Oldest First", the oldest todo item should appear on top. 
  - Select "Newest First", the oldest todo should appear on the bottom.  
 
🚀 **Now our To-Do List supports sorting!** Next, we will add **[Stretch Goals](8-stretch-goals.md)**, such as due dates and priorities. 🚀
