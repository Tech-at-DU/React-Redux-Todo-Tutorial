# **Stretch Goals: Expanding the To-Do List App**

The ideas here are only partial solutions! You will need to use the ideas from that you have learned in this tutorial to make them fully functional. Use the AI to ask questions about solving these solution if you get stuck! 

Now that our **To-Do List app is fully functional**, let’s explore **advanced features** to make it more robust and useful. These stretch goals will:
- ✅ Introduce **due dates** and **priorities** for tasks.  
- ✅ Allow users to **categorize** tasks into different lists.  
- ✅ Integrate an **external API** for task syncing.  
- ✅ Improve **performance and testing** for real-world use.  

---

## **1️⃣ Adding Due Dates**
Tasks often have **deadlines**, so let’s allow users to set a **due date** for each task.

### **Step 1: Modify `todoSlice.js` to Support Due Dates**
Open `redux/todoSlice.js` and update the `initialState` and `addTodo` reducer:

```js
const initialState = [
  { 
    id: 1, 
    text: "Learn Redux", 
    completed: false, 
    dueDate: "2024-12-31" // 1️⃣ Include a date in the default todo
  },
];
```

```JS
// Update addTodo
addTodo: (state, action) => {
  state.push({
    id: Date.now(),
    text: action.payload.text,
    completed: false,
    dueDate: action.payload.dueDate, // 2️⃣ Store due date
  });
},
```

📌 **What This Does:**  
- ✅ Adds a `dueDate` field to each task.  
- ✅ Allows users to specify a deadline when adding tasks. 
- ‼ Notice we are getting `text` and `dueDate` from `payload`. When before `text` was stored as `payload`!

**📌 AI Prompt:** "Explain the code block: <include the code above>".

### **Step 2: Modify `AddTodo.js` to Accept Due Dates**
Update `AddTodo.js` to include a date input field:

Store the date for a form element:
```js
const [dueDate, setDueDate] = useState("");
```

Add an input for the date: 
```js
<form onSubmit={handleSubmit}>
  <input
    type="text"
    value={text}
    onChange={(e) => setText(e.target.value)}
  />
  {/* Add Date picker */}
  <input 
    type="date"
    value={dueDate}
    onChange={(e) => setDueDate(e.target.value)}
  />
  <button type="submit">Add Todo</button>
</form>
```

Update `handleSubmit`:
```JS
const handleSubmit = (e) => {
  e.preventDefault();
  if (text.trim()) {
    // Wrap text and dueDate in an object
    dispatch(addTodo({ text, dueDate }));
    setText("");
  }
};
```

📌 **What This Does:**  
- ✅ Allows users to **pick a due date** when creating a task. 

**📌 AI Prompt:** "Why do I need to wrap text and dueDate in an object? <include the code above>".

---

## **2️⃣ Implementing Task Priorities**
### **Step 3: Modify `todoSlice.js` to Support Priorities**
Update `initialState` to include a **priority** field:

```js
const initialState = [
  { 
    id: 1, 
    text: "Learn Redux", 
    completed: false, 
    dueDate: "2024-12-31",
    priority: 'High' // 1️⃣ Include a date in the default todo
  }
];
```

Modify `addTodo` reducer:

```js
addTodo: (state, action) => {
  state.push({
    id: Date.now(),
    text: action.payload.text,
    completed: false,
    priority: action.payload.priority, // Store priority
  });
},
```

📌 **What This Does:**  
- ✅ Adds a `priority` field (`Low`, `Medium`, `High`).  
- ✅ Allows users to **prioritize tasks** when adding them.  

### **Step 4: Modify `AddTodo.js` to Include Priority Selection**

```js
<select value={priority} onChange={(e) => setPriority(e.target.value)}>
  <option value="Low">Low</option>
  <option value="Medium">Medium</option>
  <option value="High">High</option>
</select>
```

📌 **What This Does:**  
- ✅ Allows users to assign **importance levels** to tasks.  

---

## **3️⃣ Categorizing Tasks**
Users may want to **group tasks into categories** (Work, Personal, Errands, etc.).

### **Step 5: Modify `todoSlice.js` to Include Categories**
Add a `category` field:

```js
const initialState = [
  { id: 1, text: "Learn Redux", completed: false, category: "Work" },
];
```

Modify `addTodo` reducer:

```js
addTodo: (state, action) => {
  state.push({
    id: Date.now(),
    text: action.payload.text,
    completed: false,
    category: action.payload.category,
  });
},
```

📌 **What This Does:**  
- ✅ Allows users to **group tasks into categories**.  

### **Step 6: Modify `AddTodo.js` to Allow Category Selection**

```js
<select value={category} onChange={(e) => setCategory(e.target.value)}>
  <option value="Work">Work</option>
  <option value="Personal">Personal</option>
  <option value="Errands">Errands</option>
</select>
```

📌 **What This Does:**  
- ✅ Users can **organize tasks** into meaningful lists. 

**Stretch stretch goal:** Make the category list customizable! 

---

## **4️⃣ Syncing with an External API**
### **Step 7: Fetch and Store Tasks in an API**
Instead of storing tasks **locally**, we can **sync them with an API**.

Modify `store.js` to **fetch tasks from an API on startup**:

```js
const fetchTodos = async () => {
  const response = await fetch("https://jsonplaceholder.typicode.com/todos");
  const data = await response.json();
  return data.slice(0, 10); // Limit to 10 tasks
};

const preloadedState = { todos: await fetchTodos() };
```

📌 **What This Does:**  
- ✅ Loads tasks **from an external API** instead of LocalStorage.  
- ✅ Can be expanded to support **real-time updates**.  

To solve this problem you'll need to create a database and a localhost server to store your todo items. 

---

## **5️⃣ Performance & Testing Enhancements**
### **Step 8: Improve Performance with Memoization**
Use **`useMemo`** to prevent unnecessary re-renders in `TodoList.js`:

```js
const filteredTodos = useMemo(() => {
  return todos.filter(todo => (filter === "completed" ? todo.completed : true));
}, [todos, filter]);
```

📌 **What This Does:**  
- ✅ **Optimizes filtering performance** for large task lists.  

### **Step 9: Write Unit Tests for Redux Logic**
Install Jest and React Testing Library:

```sh
npm install --save-dev jest @testing-library/react @testing-library/react-hooks
```

Write a test for `todoSlice.js`:

```js
test("should add a new todo", () => {
  const newState = todoReducer([], addTodo({ text: "Test Todo" }));
  expect(newState.length).toBe(1);
  expect(newState[0].text).toBe("Test Todo");
});
```

📌 **What This Does:**  
- ✅ Ensures **Redux reducers work as expected**.  

📌 **AI Prompt:** "What are unit tests and why do we use them?"

📌 **AI Prompt:** "What is Jest?"

📌 **AI Prompt:** "How do I define and run unit tests?"

📌 **AI Prompt:** "When writing unit tests, what should I test?"

---

🚀 **Now our To-Do List has advanced features!** Next, we will wrap up the tutorial with **[Final Thoughts & Next Steps](9-final-thoughs.md)**. 🚀
