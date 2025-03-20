# **Adding Task Filtering & Sorting**

Now that our **To-Do List app persists tasks**, we can improve usability by adding **filtering and sorting options**. This step will:  
- ✅ Allow users to **filter tasks** (All, Completed, Active).  
- ✅ Allow users to **sort tasks** (Newest first, Oldest first).  
- ✅ Introduce Redux state for **visibility filters**.  

---

## **1️⃣ Define Filter Options in Redux**
We need Redux to **store and manage the current filter selection**.

### **Step 1: Create `filtersSlice.js`**
Inside `redux/`, create a new file **`filtersSlice.js`** and add:

```js
import { createSlice } from "@reduxjs/toolkit";

export const FILTERS = {
  ALL: "all",
  ACTIVE: "active",
  COMPLETED: "completed",
};

const filtersSlice = createSlice({
  name: "filters",
  initialState: FILTERS.ALL,
  reducers: {
    setFilter: (state, action) => action.payload,
  },
});

export const { setFilter } = filtersSlice.actions;
export default filtersSlice.reducer;
```

**📌 What This Does:**
- ✅ Defines filter options (ALL, ACTIVE, COMPLETED).
- ✅ Stores current filter selection in Redux state.
- ✅ Provides setFilter action to update the filter.

**📌 AI Debugging Prompt:** “Why do we store filters in Redux instead of component state?”

---

## 2️⃣ Update Store to Include Filters

Now, we need to add filtersSlice to the Redux store.

### Step 2: Modify `store.js` to Include Filters

Open `redux/store.js` and update it:

```JS
import { configureStore } from "@reduxjs/toolkit";
import todoReducer from "./todoSlice";
import filtersReducer from "./filtersSlice";
import { loadState, saveState } from "./localStorage";

const preloadedState = loadState();

export const store = configureStore({
  reducer: {
    todos: todoReducer,
    filters: filtersReducer,
  },
  preloadedState,
});

store.subscribe(() => {
  saveState({
    todos: store.getState().todos,
    filters: store.getState().filters,
  });
});
```

**📌 What This Does:**
- ✅ Adds filtersReducer to Redux store.
- ✅ Saves filter selection in LocalStorage.

**📌 AI Debugging Prompt:** “Why do we include filters in the persisted state?”

---

## 3️⃣ Create a Filter Selection UI

Now, we need a dropdown menu to let users select a filter.

### Step 3: Create FilterControls.js

Inside `src/components/`, create a new file `FilterControls.js` and add:

```JS
import React from "react";
import { useDispatch, useSelector } from "react-redux";
import { setFilter, FILTERS } from "../redux/filtersSlice";

const FilterControls = () => {
  const dispatch = useDispatch();
  const currentFilter = useSelector((state) => state.filters);

  return (
    <select value={currentFilter} onChange={(e) => dispatch(setFilter(e.target.value))}>
      <option value={FILTERS.ALL}>All</option>
      <option value={FILTERS.ACTIVE}>Active</option>
      <option value={FILTERS.COMPLETED}>Completed</option>
    </select>
  );
};

export default FilterControls;
```

**📌 What This Does:**
- ✅ Displays a dropdown with filter options.
- ✅ Uses useDispatch to update Redux state when the filter changes.
- ✅ Uses useSelector to track current filter in Redux state.

**📌 AI Debugging Prompt:** “Why does changing the filter update the UI instantly?”

---

## 4️⃣ Apply Filtering to Todo List

Now, let’s modify `TodoList.js` to only display tasks based on the selected filter.

### Step 4: Modify TodoList.js to Use Filters

Open `TodoList.js` and update it:

```JS
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { toggleComplete, deleteTodo } from "../redux/todoSlice";
import { FILTERS } from "../redux/filtersSlice";
import "../App.css";

const TodoList = () => {
  const todos = useSelector((state) => state.todos);
  const filter = useSelector((state) => state.filters);
  const dispatch = useDispatch();

  const filteredTodos = todos.filter((todo) => {
    if (filter === FILTERS.ACTIVE) return !todo.completed;
    if (filter === FILTERS.COMPLETED) return todo.completed;
    return true; // ALL
  });

  return (
    <ul>
      {filteredTodos.map((todo) => (
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

**📌 What This Does:**
- ✅ Filters the to-do list based on the selected option.
- ✅ Updates the list instantly when the filter changes.

**📌 AI Debugging Prompt:** “Why do tasks disappear when selecting a filter?”

---

## 5️⃣ Update `App.js` to Include Filters

Finally, let’s import FilterControls.js into App.js.

### Step 5: Modify `App.js` to Include Filters

Open `App.js` and update it:

```JS
import React from "react";
import AddTodo from "./components/AddTodo";
import TodoList from "./components/TodoList";
import FilterControls from "./components/FilterControls";

function App() {
  return (
    <div>
      <h1>Redux To-Do List</h1>
      <AddTodo />
      <FilterControls />
      <TodoList />
    </div>
  );
}

export default App;
```

**📌 What This Does:**
- ✅ Adds filter controls above the to-do list.
- ✅ Allows users to change filters easily.

**📌 AI Debugging Prompt:** “Why does my filter selection reset after a refresh?”

---

🚀 Now our To-Do List supports filtering! Next, we will add sorting options so users can reorder tasks based on date or completion status. 🚀
