# **Enhancing the UI & Adding Interactivity**

Now that our **Redux state is connected to React**, we can improve the **user experience** by enhancing the UI and adding interactivity. This step will:  
- ✅ Improve layout and styling.  
- ✅ Provide user feedback (highlight completed tasks).  
- ✅ Add transitions for smooth UI updates.  

---

## **1️⃣ Add Basic Styling**
To improve the appearance of the app, create a new **CSS file**.

### **Step 1: Create `App.css`**
Inside `src/`, create a file **`App.css`** and add:

```css
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  text-align: center;
  margin: 0;
  padding: 20px;
}

h1 {
  color: #333;
}

form {
  margin-bottom: 20px;
}

input {
  padding: 10px;
  margin-right: 10px;
}

button {
  padding: 10px;
  cursor: pointer;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  background: #fff;
  padding: 10px;
  margin: 5px auto;
  width: 300px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 5px;
  transition: background 0.3s ease;
}

.completed {
  text-decoration: line-through;
  color: gray;
}
```

**📌 What This Does:**
- ✅ Styles buttons, inputs, and the list layout.
- ✅ Adds smooth background transitions for better UX.
- ✅ Highlights completed tasks using the .completed class.

---

## 2️⃣ Update Components to Use Styling

### Step 2: Import CSS in `App.js`

Modify `App.js` to include the styles:

```JS
import "./App.css";
```

Now, let’s update the `TodoList` to apply the completed task styles.

---

## Step 3: Modify `TodoList.js`

Update `TodoList.js` to apply styles dynamically:

```JS
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { toggleComplete, deleteTodo } from "../redux/todoSlice";
import "../App.css";

const TodoList = () => {
  const todos = useSelector((state) => state.todos);
  const dispatch = useDispatch();

  return (
    <ul>
      {todos.map((todo) => (
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
- ✅ Uses CSS classes (completed) to visually style completed tasks.
- ✅ Keeps task items styled properly.

**📌 AI Debugging Prompt:** “Why might my styles not be applying correctly? How can I check if the CSS is loaded?”

---

## 3️⃣ Add Task Animations for Better UX

To enhance interactivity, let’s add CSS transitions.

### Step 4: Modify `App.css` to Include Transitions

Update `App.css` with the following:

```CSS
li {
  opacity: 1;
  transform: scale(1);
  transition: opacity 0.3s ease, transform 0.3s ease;
}

li.removed {
  opacity: 0;
  transform: scale(0.8);
}
```

Now, update `TodoList.js` to use the removed animation.

## Step 5: Update `TodoList.js` to Animate Deletions

Modify `TodoList.js` to temporarily apply the removal effect before deleting a task:

```JS
const handleDelete = (id) => {
  const item = document.getElementById(`todo-${id}`);
  if (item) {
    item.classList.add("removed");
    setTimeout(() => dispatch(deleteTodo(id)), 300);
  } else {
    dispatch(deleteTodo(id));
  }
};
```

Then, update the list item to include an id:

```js
<li key={todo.id} id={`todo-${todo.id}`} className={todo.completed ? "completed" : ""}>
```

**📌 What This Does:**
- ✅ Smoothly animates task deletion.
- ✅ Prevents instant disappearance for better UX.

**📌 AI Debugging Prompt:** “Why doesn’t my animation work on task deletion?”

---

🚀 Now our To-Do List has improved UI and animations! Next, we will implement **[State Persistence](5-presistence.md)** so tasks remain after a page refresh. 🚀
