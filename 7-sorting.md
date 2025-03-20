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
import sortingReducer from "./sortingSlice";
import { loadState, saveState } from "./localStorage";

const preloadedState = loadState();

export const store = configureStore({
  reducer: {
    todos: todoReducer,
    filters: filtersReducer,
    sorting: sortingReducer,
  },
  preloadedState,
});

store.subscribe(() => {
  saveState({
    todos: store.getState().todos,
    filters: store.getState().filters,
    sorting: store.getState().sorting,
  });
});
```

📌 **What This Does:**  
- ✅ Adds `sortingReducer` to Redux store.  
- ✅ Saves **sorting preferences** in LocalStorage.  

📌 **AI Debugging Prompt:** *"Why do we include sorting in the persisted state?"*

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

📌 **AI Debugging Prompt:** *"Why does changing sorting instantly update the task order?"*

---

## **4️⃣ Apply Sorting to Todo List**
Now, let’s modify `TodoList.js` to **sort tasks** before rendering.

### **Step 4: Modify `TodoList.js` to Apply Sorting**
Open `TodoList.js` and update it:

```js
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { toggleComplete, deleteTodo } from "../redux/todoSlice";
import { FILTERS } from "../redux/filtersSlice";
import { SORTING } from "../redux/sortingSlice";
import "../App.css";

const TodoList = () => {
  const todos = useSelector((state) => state.todos);
  const filter = useSelector((state) => state.filters);
  const sorting = useSelector((state) => state.sorting);
  const dispatch = useDispatch();

  const filteredTodos = todos.filter((todo) => {
    if (filter === FILTERS.ACTIVE) return !todo.completed;
    if (filter === FILTERS.COMPLETED) return todo.completed;
    return true; // ALL
  });

  const sortedTodos = [...filteredTodos].sort((a, b) => {
    if (sorting === SORTING.NEWEST_FIRST) return b.id - a.id;
    if (sorting === SORTING.OLDEST_FIRST) return a.id - b.id;
    if (sorting === SORTING.COMPLETED_FIRST) return b.completed - a.completed;
    return 0;
  });

  return (
    <ul>
      {sortedTodos.map((todo) => (
        <li key={todo.id} className={todo.completed ? "completed" : ""}>
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

📌 **What This Does:**  
- ✅ Filters tasks **first**, then sorts them based on selected option.  
- ✅ Ensures **tasks update instantly** when sorting changes.  

📌 **AI Debugging Prompt:** *"Why does my sorting not update properly?"*

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
import SortingControls from "./components/SortingControls";

function App() {
  return (
    <div>
      <h1>Redux To-Do List</h1>
      <AddTodo />
      <FilterControls />
      <SortingControls />
      <TodoList />
    </div>
  );
}

export default App;
```

🚀 **Now our To-Do List supports sorting!** Next, we will add **[Stretch Goals](8-stretch-goals.md)**, such as due dates and priorities. 🚀
