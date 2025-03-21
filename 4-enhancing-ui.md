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
  transition: background 0.3s ease, opacity 0.3s ease, transform 0.3s ease;
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.removed {
  opacity: 0;
  transform: scale(0.8);
}
```

**📌 What This Does:**
- ✅ Styles buttons, inputs, and the list layout.
- ✅ Adds smooth background transitions for better UX.
- ✅ Highlights completed tasks using the `.completed` class.

---

## **2️⃣ Update Components to Use Styling**

### **Step 2: Import CSS in `App.js`**
Modify `App.js` to include the styles:

```js
import "./App.css";
```

Now, let’s update the `TodoList` to apply the completed task styles.

---

## **3️⃣ Modify `TodoList.js`**

Update `TodoList.js` to apply styles dynamically and properly handle deletions:

```js
import React, { useState } from "react";
import { useSelector, useDispatch } from "react-redux";
import { toggleComplete, deleteTodo } from "../redux/todoSlice";

const TodoList = () => {
  const todos = useSelector((state) => state.todos);
  const dispatch = useDispatch();
  const [removingId, setRemovingId] = useState(null);

  const handleDelete = (id) => {
    setRemovingId(id);
    setTimeout(() => {
      dispatch(deleteTodo(id));
      setRemovingId(null);
    }, 300);
  };

  return (
    <ul>
      {todos.map((todo) => (
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

**📌 What This Does:**
- ✅ Ensures **smooth removal animation** before actually deleting the task.
- ✅ Keeps task items styled properly.

**📌 AI Debugging Prompt:** “Why might my styles not be applying correctly? How can I check if the CSS is loaded?”

**📌 AI Prompt:** “Explain the process of deleting a todo item in the code sample: <include the code above>”

**📌 AI Prompt:** “What is a stale closure? Does the code provided have the potential for 'stale closure'? <include the code above>”

---

🚀 Now our To-Do List has improved UI and animations while following **React best practices**! Next, we will implement **[State Persistence](5-presistence.md)** so tasks remain after a page refresh. 🚀
