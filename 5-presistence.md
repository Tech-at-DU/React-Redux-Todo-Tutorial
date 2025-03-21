# **Adding State Persistence (LocalStorage)**

Now that our **To-Do List app looks and feels great**, we need to make sure tasks **persist** when the page is refreshed. Right now, all tasks **reset** when the user reloads the page because Redux state is stored in memory.

- ✅ We will **save tasks to LocalStorage** so they remain even after a page refresh.  
- ✅ We will **load saved tasks** when the app starts.  
- ✅ We will **update LocalStorage** whenever the Redux state changes.  

---

## **1️⃣ Save State to LocalStorage**
We need a way to **save the to-do list** whenever it changes.

### **Step 1: Create a Utility Function**
Inside `redux/`, create a new file **`localStorage.js`** and add:

```js
export const saveState = (state) => {
  try {
    localStorage.setItem("todos", JSON.stringify(state));
  } catch (error) {
    console.error("Error saving state to LocalStorage:", error);
  }
};

export const loadState = () => {
  try {
    const savedTodos = localStorage.getItem("todos");
    return savedTodos ? { todos: JSON.parse(savedTodos) } : undefined; // Ensure it returns an object
  } catch (error) {
    console.error("Error loading state from LocalStorage:", error);
    return undefined;
  }
};
```

**📌 What This Does:**
- ✅ `saveState(state)` – Saves the Redux state to LocalStorage.
- ✅ `loadState()` – Loads previously saved state when the app starts.
- ✅ Handles errors in case LocalStorage fails.

**📌 AI Prompt:** “What is 'localStorage'?”

**📌 AI Prompt:** “What can localStorage be used for? Does it have any limitations?”

**📌 AI Prompt:** “Why do we use JSON.stringify and JSON.parse when working with LocalStorage?”

---

## 2️⃣ Load Saved State When App Starts

Now, we will load the saved to-do list when Redux initializes.

### Step 2: Modify `store.js` to Load Initial State

Open `redux/store.js` and update it:

```JS
import { configureStore } from "@reduxjs/toolkit";
import todoReducer from "./todoSlice";
import { loadState, saveState } from "./localStorage";

const preloadedState = loadState() || { todos: [] }; 

export const store = configureStore({
  reducer: {
    todos: todoReducer,
  },
  preloadedState, 
});

store.subscribe(() => {
  saveState(store.getState().todos); // Still saving only the todos array
});
```

**📌 What This Does:**
- ✅ Loads the previous state from LocalStorage when Redux starts.
- ✅ Automatically saves state whenever Redux state updates.

**📌 AI Prompt:** “What is `store.subscribe` doing here? <inlcude code above>”

**📌 AI Prompt:** “Where does `preloadedState` come from?”

---

## 3️⃣ Test State Persistence

### Step 3: Open the App and Try It!

- ✅ Add a few tasks to the To-Do List.
- ✅ Refresh the page and check if the tasks are still there.
- ✅ Try marking tasks as completed and refreshing again.

**📌 Expected Behavior:** Tasks should persist even after refreshing.

**📌 AI Debugging Prompt:** “My tasks disappear after refresh. What might be going wrong?”

---

🚀 Now our To-Do List persists across page reloads! Next, we will add **[Filtering Tasks](6-filtering-and-sorting.md)** to organize tasks more effectively. 🚀
