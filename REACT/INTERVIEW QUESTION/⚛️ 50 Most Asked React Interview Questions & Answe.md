
# ⚛️ 50 Most Asked React Interview Questions \& Answers

## 📋 Table of Contents

| \# | Question Category | Quick Link |
| :-- | :-- | :-- |
| [1-10](#basic-react-concepts-) | Basic React Concepts | Foundation knowledge |
| [11-20](#jsx-and-components-) | JSX \& Components | Core building blocks |
| [21-30](#state-and-props-) | State \& Props | Data management |
| [31-40](#hooks-and-lifecycle-) | Hooks \& Lifecycle | Modern React patterns |
| [41-50](#advanced-concepts-) | Advanced Concepts | Expert-level topics |


***

## Basic React Concepts 🚀

<details>
<summary><strong>1. What is React and why is it popular?</strong></summary>

**Answer:**
React is a JavaScript library for building user interfaces, especially web applications. Created by Facebook, it's popular because it uses a component-based architecture, virtual DOM for performance, and has a large ecosystem. React makes building interactive UIs predictable and efficient.[^1][^2]

**Example:**
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';

// Simple React component
function App() {
  return (
    <div>
      <h1>Hello, React!</h1>
      <p>Welcome to React development</p>
    </div>
  );
}

// Render to DOM
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```
</details>
<details>
<summary><strong>2. What is the Virtual DOM and how does it work?</strong></summary>

**Answer:**
The Virtual DOM is a lightweight representation of the real DOM stored in memory. When state changes, React creates a new virtual DOM tree, compares it with the previous tree (diffing), and updates only the changed parts in the real DOM (reconciliation). This makes React fast and efficient.[^2][^3]

**Example:**
```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  // When count changes, Virtual DOM compares and updates only the counter text
  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```
</details>
<details>
<summary><strong>3. What are React components?</strong></summary>

**Answer:**
Components are the building blocks of React applications. They are reusable pieces of UI that can accept inputs (props) and return React elements. Components split the UI into independent, reusable parts that can be processed separately.[^4][^2]

**Example:**
```jsx
// Functional Component
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Class Component
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}

// Using the component
function App() {
  return (
    <div>
      <Welcome name="John" />
      <Welcome name="Sarah" />
    </div>
  );
}
```
</details>
<details>
<summary><strong>4. What's the difference between functional and class components?</strong></summary>

**Answer:**
Functional components are JavaScript functions that return JSX and use hooks for state management. Class components are ES6 classes that extend React.Component and have their own state and lifecycle methods. Functional components are simpler and the modern preferred approach.[^2][^4]

**Example:**
```jsx
// Functional Component (Modern approach)
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);
  
  return user ? <div>Hello {user.name}</div> : <div>Loading...</div>;
}

// Class Component (Traditional approach)
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = { user: null };
  }
  
  componentDidMount() {
    fetchUser(this.props.userId)
      .then(user => this.setState({ user }));
  }
  
  render() {
    return this.state.user ? 
      <div>Hello {this.state.user.name}</div> : 
      <div>Loading...</div>;
  }
}
```
</details>
<details>
<summary><strong>5. What is JSX?</strong></summary>

**Answer:**
JSX (JavaScript XML) is a syntax extension for JavaScript that allows you to write HTML-like code in JavaScript. It gets transpiled to React.createElement() calls by tools like Babel. JSX makes components more readable and easier to write.[^3][^2]

**Example:**
```jsx
// JSX syntax
const element = <h1 className="greeting">Hello, world!</h1>;

// Transpiles to
const element = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);

// JSX with expressions
function Greeting({ name, time }) {
  return (
    <div>
      <h1>Hello {name}!</h1>
      <p>Current time: {new Date(time).toLocaleTimeString()}</p>
      {time > 18 && <p>Good evening!</p>}
    </div>
  );
}
```
</details>
<details>
<summary><strong>6. How do you handle events in React?</strong></summary>

**Answer:**
React uses SyntheticEvents, which are wrappers around native events that provide consistent behavior across browsers. Event handlers are passed as props and use camelCase naming. React automatically binds `this` in functional components.[^3][^2]

**Example:**
```jsx
function EventExamples() {
  const [inputValue, setInputValue] = useState('');
  const [isSubmitted, setIsSubmitted] = useState(false);
  
  // Handle input change
  const handleChange = (e) => {
    setInputValue(e.target.value);
  };
  
  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    setIsSubmitted(true);
    console.log('Form submitted with:', inputValue);
  };
  
  // Handle button click
  const handleClick = (message) => {
    alert(message);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input 
        type="text" 
        value={inputValue}
        onChange={handleChange}
        placeholder="Enter text"
      />
      <button type="submit">Submit</button>
      <button 
        type="button"
        onClick={() => handleClick('Button clicked!')}
      >
        Click Me
      </button>
      {isSubmitted && <p>Form submitted successfully!</p>}
    </form>
  );
}
```
</details>
<details>
<summary><strong>7. What are keys in React and why are they important?</strong></summary>

**Answer:**
Keys are special attributes that help React identify which items have changed, been added, or removed in lists. They should be unique and stable. Keys help React optimize rendering performance by reusing existing DOM elements when possible.[^2]

**Example:**
```jsx
function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: 'Learn React', completed: false },
    { id: 2, text: 'Build an app', completed: false },
    { id: 3, text: 'Deploy to production', completed: true }
  ]);
  
  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };
  
  return (
    <ul>
      {todos.map(todo => (
        <li 
          key={todo.id} // ✅ Good: stable, unique key
          onClick={() => toggleTodo(todo.id)}
          style={{ 
            textDecoration: todo.completed ? 'line-through' : 'none' 
          }}
        >
          {todo.text}
        </li>
      ))}
    </ul>
  );
}

// ❌ Bad: using array index as key
// {todos.map((todo, index) => <li key={index}>...
```
</details>
<details>
<summary><strong>8. What is conditional rendering in React?</strong></summary>

**Answer:**
Conditional rendering allows you to render different components or elements based on certain conditions. You can use JavaScript operators like if statements, ternary operators, or logical AND operator to conditionally show content.[^2]

**Example:**
```jsx
function UserDashboard({ user, isLoggedIn }) {
  // Method 1: if-else statement
  if (!isLoggedIn) {
    return <LoginForm />;
  }
  
  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      
      {/* Method 2: Ternary operator */}
      {user.isAdmin ? (
        <AdminPanel />
      ) : (
        <UserPanel />
      )}
      
      {/* Method 3: Logical AND operator */}
      {user.hasNotifications && <NotificationBell />}
      
      {/* Method 4: Function-based conditional rendering */}
      {renderUserStatus()}
    </div>
  );
  
  function renderUserStatus() {
    switch (user.status) {
      case 'premium':
        return <PremiumFeatures />;
      case 'trial':
        return <TrialExpiration />;
      default:
        return <BasicFeatures />;
    }
  }
}
```
</details>
<details>
<summary><strong>9. How do you style components in React?</strong></summary>

**Answer:**
React components can be styled using CSS files, inline styles, CSS modules, styled-components, or CSS-in-JS libraries. Each approach has its benefits depending on the use case and project requirements.[^4]

**Example:**
```jsx
import React from 'react';
import './Button.css'; // External CSS
import styles from './Button.module.css'; // CSS Modules

function StyledComponents() {
  // Inline styles
  const inlineStyle = {
    backgroundColor: '#007bff',
    color: 'white',
    padding: '10px 20px',
    border: 'none',
    borderRadius: '4px',
    cursor: 'pointer'
  };
  
  return (
    <div>
      {/* Method 1: Inline styles */}
      <button style={inlineStyle}>
        Inline Styled Button
      </button>
      
      {/* Method 2: CSS Classes */}
      <button className="btn btn-primary">
        CSS Class Button
      </button>
      
      {/* Method 3: CSS Modules */}
      <button className={styles.moduleButton}>
        CSS Module Button
      </button>
      
      {/* Method 4: Dynamic styles */}
      <button 
        style={{
          ...inlineStyle,
          backgroundColor: isActive ? '#28a745' : '#6c757d'
        }}
      >
        Dynamic Button
      </button>
    </div>
  );
}

// CSS Modules (Button.module.css)
/*
.moduleButton {
  background-color: #17a2b8;
  color: white;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 0.25rem;
}

.moduleButton:hover {
  background-color: #138496;
}
*/
```
</details>
<details>
<summary><strong>10. What are React Fragments?</strong></summary>

**Answer:**
React Fragments let you group multiple elements without adding extra nodes to the DOM. They solve the problem of having to wrap multiple elements in a single parent div, which can create unnecessary HTML structure.[^4]

**Example:**
```jsx
import React, { Fragment } from 'react';

function UserInfo({ user }) {
  // Method 1: React.Fragment
  return (
    <Fragment>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <p>{user.phone}</p>
    </Fragment>
  );
}

function UserList({ users }) {
  // Method 2: Short syntax <>
  return (
    <>
      <h1>User List</h1>
      {users.map(user => (
        <Fragment key={user.id}>
          <UserInfo user={user} />
          <hr />
        </Fragment>
      ))}
    </>
  );
}

// Without Fragments (creates extra div)
function BadExample() {
  return (
    <div> {/* Unnecessary wrapper */}
      <h2>Title</h2>
      <p>Content</p>
    </div>
  );
}

// With Fragments (no extra DOM node)
function GoodExample() {
  return (
    <>
      <h2>Title</h2>
      <p>Content</p>
    </>
  );
}
```
</details>

***

## JSX and Components 🧩

<details>
<summary><strong>11. What are props in React?</strong></summary>

**Answer:**
Props (properties) are read-only inputs passed to components from their parent. They allow data to flow down from parent to child components. Props make components reusable by allowing them to render different data.[^3][^2]

**Example:**
```jsx
// Child component receiving props
function UserCard({ name, email, avatar, isOnline, onSendMessage }) {
  return (
    <div className="user-card">
      <img src={avatar} alt={`${name}'s avatar`} />
      <div>
        <h3>{name}</h3>
        <p>{email}</p>
        <span className={isOnline ? 'online' : 'offline'}>
          {isOnline ? '🟢 Online' : '🔴 Offline'}
        </span>
        <button onClick={() => onSendMessage(name)}>
          Send Message
        </button>
      </div>
    </div>
  );
}

// Parent component passing props
function UserList() {
  const users = [
    {
      id: 1,
      name: 'John Doe',
      email: 'john@example.com',
      avatar: '/avatars/john.jpg',
      isOnline: true
    },
    {
      id: 2,
      name: 'Jane Smith',
      email: 'jane@example.com',
      avatar: '/avatars/jane.jpg',
      isOnline: false
    }
  ];
  
  const handleSendMessage = (userName) => {
    alert(`Sending message to ${userName}`);
  };
  
  return (
    <div>
      {users.map(user => (
        <UserCard
          key={user.id}
          name={user.name}
          email={user.email}
          avatar={user.avatar}
          isOnline={user.isOnline}
          onSendMessage={handleSendMessage}
        />
      ))}
    </div>
  );
}
```
</details>
<details>
<summary><strong>12. What is prop destructuring?</strong></summary>

**Answer:**
Prop destructuring is a JavaScript feature that allows you to extract specific properties from props object directly in the function parameters. This makes the code cleaner and more readable by avoiding repetitive `props.propertyName` syntax.

**Example:**
```jsx
// Without destructuring
function UserProfile(props) {
  return (
    <div>
      <h2>{props.name}</h2>
      <p>Email: {props.email}</p>
      <p>Age: {props.age}</p>
      <p>Role: {props.role}</p>
    </div>
  );
}

// With destructuring in parameters
function UserProfile({ name, email, age, role }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Email: {email}</p>
      <p>Age: {age}</p>
      <p>Role: {role}</p>
    </div>
  );
}

// Destructuring with default values
function UserProfile({ 
  name, 
  email, 
  age = 'Not specified', 
  role = 'User',
  isActive = false 
}) {
  return (
    <div className={isActive ? 'active-user' : 'inactive-user'}>
      <h2>{name}</h2>
      <p>Email: {email}</p>
      <p>Age: {age}</p>
      <p>Role: {role}</p>
    </div>
  );
}

// Destructuring with rest operator
function UserProfile({ name, email, ...otherProps }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Email: {email}</p>
      <div>
        Other info: {JSON.stringify(otherProps)}
      </div>
    </div>
  );
}
```
</details>
<details>
<summary><strong>13. What are default props?</strong></summary>

**Answer:**
Default props provide fallback values for props when they are not passed by the parent component. They ensure components work correctly even when some props are missing and help define expected prop types.[^2]

**Example:**
```jsx
// Method 1: Using defaultProps (Class components)
class Button extends React.Component {
  render() {
    return (
      <button 
        className={this.props.className}
        disabled={this.props.disabled}
        onClick={this.props.onClick}
      >
        {this.props.children}
      </button>
    );
  }
}

Button.defaultProps = {
  className: 'btn',
  disabled: false,
  children: 'Click me',
  onClick: () => console.log('Button clicked')
};

// Method 2: Default parameters (Functional components)
function Button({
  className = 'btn',
  disabled = false,
  children = 'Click me',
  onClick = () => console.log('Button clicked'),
  variant = 'primary'
}) {
  return (
    <button 
      className={`${className} ${className}-${variant}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

// Method 3: Using defaultProps with functional components
function Card({ title, content, showFooter, footerText }) {
  return (
    <div className="card">
      <h3>{title}</h3>
      <p>{content}</p>
      {showFooter && <footer>{footerText}</footer>}
    </div>
  );
}

Card.defaultProps = {
  title: 'Default Title',
  content: 'Default content',
  showFooter: false,
  footerText: 'Default footer'
};

// Usage
function App() {
  return (
    <div>
      <Button />
      <Button disabled>Disabled</Button>
      <Button variant="secondary">Secondary</Button>
      
      <Card />
      <Card title="Custom Title" showFooter footerText="Custom Footer" />
    </div>
  );
}
```
</details>
<details>
<summary><strong>14. How do you pass data from child to parent component?</strong></summary>

**Answer:**
Data flows from parent to child through props, but to send data from child to parent, you pass callback functions as props. The child component calls these functions with data as arguments, allowing communication upward in the component tree.

**Example:**
```jsx
// Child Component
function AddTodo({ onAddTodo }) {
  const [inputValue, setInputValue] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (inputValue.trim()) {
      // Send data to parent via callback
      onAddTodo({
        id: Date.now(),
        text: inputValue,
        completed: false
      });
      setInputValue(''); // Clear input
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
        placeholder="Enter todo..."
      />
      <button type="submit">Add Todo</button>
    </form>
  );
}

// Parent Component
function TodoApp() {
  const [todos, setTodos] = useState([]);
  
  // Callback function to receive data from child
  const handleAddTodo = (newTodo) => {
    setTodos(prevTodos => [...prevTodos, newTodo]);
  };
  
  const handleToggleTodo = (todoId) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === todoId 
          ? { ...todo, completed: !todo.completed }
          : todo
      )
    );
  };
  
  const handleDeleteTodo = (todoId) => {
    setTodos(prevTodos => prevTodos.filter(todo => todo.id !== todoId));
  };
  
  return (
    <div>
      <h1>Todo App</h1>
      <AddTodo onAddTodo={handleAddTodo} />
      
      <TodoList 
        todos={todos}
        onToggle={handleToggleTodo}
        onDelete={handleDeleteTodo}
      />
    </div>
  );
}

// Another child component
function TodoList({ todos, onToggle, onDelete }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={() => onToggle(todo.id)}
          onDelete={() => onDelete(todo.id)}
        />
      ))}
    </ul>
  );
}

function TodoItem({ todo, onToggle, onDelete }) {
  return (
    <li>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={onToggle}
      />
      <span style={{ 
        textDecoration: todo.completed ? 'line-through' : 'none' 
      }}>
        {todo.text}
      </span>
      <button onClick={onDelete}>Delete</button>
    </li>
  );
}
```
</details>
<details>
<summary><strong>15. What is component composition?</strong></summary>

**Answer:**
Component composition is a pattern where components are combined to create more complex components. Instead of inheritance, React uses composition to reuse code between components. This approach provides more flexibility and better component reusability.

**Example:**
```jsx
// Basic composition with children
function Card({ children, className = '' }) {
  return (
    <div className={`card ${className}`}>
      {children}
    </div>
  );
}

function CardHeader({ children }) {
  return <div className="card-header">{children}</div>;
}

function CardBody({ children }) {
  return <div className="card-body">{children}</div>;
}

function CardFooter({ children }) {
  return <div className="card-footer">{children}</div>;
}

// Higher-order composition
function Modal({ children, isOpen, onClose }) {
  if (!isOpen) return null;
  
  return (
    <div className="modal-backdrop" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>
  );
}

function ConfirmDialog({ isOpen, onClose, onConfirm, title, message }) {
  return (
    <Modal isOpen={isOpen} onClose={onClose}>
      <CardHeader>
        <h2>{title}</h2>
      </CardHeader>
      <CardBody>
        <p>{message}</p>
      </CardBody>
      <CardFooter>
        <button onClick={onClose}>Cancel</button>
        <button onClick={onConfirm}>Confirm</button>
      </CardFooter>
    </Modal>
  );
}

// Usage
function App() {
  const [showConfirm, setShowConfirm] = useState(false);
  
  const handleDelete = () => {
    console.log('Item deleted');
    setShowConfirm(false);
  };
  
  return (
    <div>
      {/* Basic composition */}
      <Card className="user-card">
        <CardHeader>
          <h2>John Doe</h2>
        </CardHeader>
        <CardBody>
          <p>Software Developer</p>
          <p>john@example.com</p>
        </CardBody>
        <CardFooter>
          <button onClick={() => setShowConfirm(true)}>
            Delete User
          </button>
        </CardFooter>
      </Card>
      
      {/* Advanced composition */}
      <ConfirmDialog
        isOpen={showConfirm}
        onClose={() => setShowConfirm(false)}
        onConfirm={handleDelete}
        title="Confirm Delete"
        message="Are you sure you want to delete this user?"
      />
    </div>
  );
}
```
</details>
<details>
<summary><strong>16. What are higher-order components (HOCs)?</strong></summary>

**Answer:**
Higher-Order Components are functions that take a component and return a new component with additional props or behavior. HOCs are a pattern for reusing component logic, similar to higher-order functions in JavaScript.[^5]

**Example:**
```jsx
// HOC for adding loading functionality
function withLoading(WrappedComponent) {
  return function WithLoadingComponent(props) {
    if (props.isLoading) {
      return <div>Loading...</div>;
    }
    return <WrappedComponent {...props} />;
  };
}

// HOC for authentication
function withAuth(WrappedComponent) {
  return function WithAuthComponent(props) {
    const [user, setUser] = useState(null);
    const [isLoading, setIsLoading] = useState(true);
    
    useEffect(() => {
      // Check authentication
      checkAuthStatus().then(user => {
        setUser(user);
        setIsLoading(false);
      });
    }, []);
    
    if (isLoading) {
      return <div>Checking authentication...</div>;
    }
    
    if (!user) {
      return <div>Please log in to access this page.</div>;
    }
    
    return <WrappedComponent {...props} user={user} />;
  };
}

// Original components
function UserList({ users, isLoading }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

function Dashboard({ user }) {
  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <p>Your dashboard content here...</p>
    </div>
  );
}

// Enhanced components using HOCs
const UserListWithLoading = withLoading(UserList);
const DashboardWithAuth = withAuth(Dashboard);

// Usage
function App() {
  const [users, setUsers] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  
  useEffect(() => {
    fetchUsers().then(users => {
      setUsers(users);
      setIsLoading(false);
    });
  }, []);
  
  return (
    <div>
      <UserListWithLoading 
        users={users} 
        isLoading={isLoading} 
      />
      <DashboardWithAuth />
    </div>
  );
}

// Multiple HOCs composition
const EnhancedDashboard = withAuth(withLoading(Dashboard));
```
</details>
<details>
<summary><strong>17. What is render prop pattern?</strong></summary>

**Answer:**
Render props is a pattern where a component receives a function as a prop that returns a React element. This function is called with data from the component, allowing for flexible and reusable component logic sharing.[^3]

**Example:**
```jsx
// Mouse tracker component using render props
class Mouse extends React.Component {
  state = { x: 0, y: 0 };
  
  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  };
  
  render() {
    return (
      <div 
        style={{ height: '100vh', width: '100vw' }}
        onMouseMove={this.handleMouseMove}
      >
        {this.props.render(this.state)}
      </div>
    );
  }
}

// Using render props
function App() {
  return (
    <div>
      <h1>Move the mouse around!</h1>
      <Mouse
        render={({ x, y }) => (
          <h2>The mouse position is ({x}, {y})</h2>
        )}
      />
      
      <Mouse
        render={({ x, y }) => (
          <div 
            style={{
              position: 'absolute',
              left: x,
              top: y,
              width: 10,
              height: 10,
              backgroundColor: 'red',
              borderRadius: '50%'
            }}
          />
        )}
      />
    </div>
  );
}

// Data fetcher component with render props
function DataFetcher({ url, render }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(response => response.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, [url]);
  
  return render({ data, loading, error });
}

// Usage with different render functions
function UserProfile({ userId }) {
  return (
    <DataFetcher
      url={`/api/users/${userId}`}
      render={({ data: user, loading, error }) => {
        if (loading) return <div>Loading user...</div>;
        if (error) return <div>Error: {error.message}</div>;
        return (
          <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
          </div>
        );
      }}
    />
  );
}
```
</details>
<details>
<summary><strong>18. How do you handle forms in React?</strong></summary>

**Answer:**
React handles forms using controlled components where form input values are controlled by React state. This provides a single source of truth and allows for validation, formatting, and dynamic behavior.[^2]

**Example:**
```jsx
import React, { useState } from 'react';

function RegistrationForm() {
  const [formData, setFormData] = useState({
    firstName: '',
    lastName: '',
    email: '',
    password: '',
    confirmPassword: '',
    age: '',
    country: '',
    interests: [],
    newsletter: false
  });
  
  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  // Handle input changes
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    
    if (type === 'checkbox') {
      if (name === 'interests') {
        // Handle multiple checkboxes
        setFormData(prev => ({
          ...prev,
          interests: checked 
            ? [...prev.interests, value]
            : prev.interests.filter(interest => interest !== value)
        }));
      } else {
        // Handle single checkbox
        setFormData(prev => ({
          ...prev,
          [name]: checked
        }));
      }
    } else {
      setFormData(prev => ({
        ...prev,
        [name]: value
      }));
    }
    
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({
        ...prev,
        [name]: ''
      }));
    }
  };
  
  // Form validation
  const validateForm = () => {
    const newErrors = {};
    
    if (!formData.firstName.trim()) {
      newErrors.firstName = 'First name is required';
    }
    
    if (!formData.lastName.trim()) {
      newErrors.lastName = 'Last name is required';
    }
    
    if (!formData.email.trim()) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }
    
    if (!formData.password) {
      newErrors.password = 'Password is required';
    } else if (formData.password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters';
    }
    
    if (formData.password !== formData.confirmPassword) {
      newErrors.confirmPassword = 'Passwords do not match';
    }
    
    if (!formData.age) {
      newErrors.age = 'Age is required';
    } else if (formData.age < 18) {
      newErrors.age = 'Must be 18 or older';
    }
    
    return newErrors;
  };
  
  // Handle form submission
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    const newErrors = validateForm();
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }
    
    setIsSubmitting(true);
    
    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 2000));
      console.log('Form submitted:', formData);
      alert('Registration successful!');
      
      // Reset form
      setFormData({
        firstName: '',
        lastName: '',
        email: '',
        password: '',
        confirmPassword: '',
        age: '',
        country: '',
        interests: [],
        newsletter: false
      });
    } catch (error) {
      console.error('Submission error:', error);
    } finally {
      setIsSubmitting(false);
    }
  };
  
  return (
    <form onSubmit={handleSubmit} className="registration-form">
      <h2>User Registration</h2>
      
      <div className="form-group">
        <label>First Name:</label>
        <input
          type="text"
          name="firstName"
          value={formData.firstName}
          onChange={handleChange}
          className={errors.firstName ? 'error' : ''}
        />
        {errors.firstName && <span className="error-message">{errors.firstName}</span>}
      </div>
      
      <div className="form-group">
        <label>Last Name:</label>
        <input
          type="text"
          name="lastName"
          value={formData.lastName}
          onChange={handleChange}
          className={errors.lastName ? 'error' : ''}
        />
        {errors.lastName && <span className="error-message">{errors.lastName}</span>}
      </div>
      
      <div className="form-group">
        <label>Email:</label>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          className={errors.email ? 'error' : ''}
        />
        {errors.email && <span className="error-message">{errors.email}</span>}
      </div>
      
      <div className="form-group">
        <label>Password:</label>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          className={errors.password ? 'error' : ''}
        />
        {errors.password && <span className="error-message">{errors.password}</span>}
      </div>
      
      <div className="form-group">
        <label>Confirm Password:</label>
        <input
          type="password"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
          className={errors.confirmPassword ? 'error' : ''}
        />
        {errors.confirmPassword && <span className="error-message">{errors.confirmPassword}</span>}
      </div>
      
      <div className="form-group">
        <label>Age:</label>
        <input
          type="number"
          name="age"
          value={formData.age}
          onChange={handleChange}
          className={errors.age ? 'error' : ''}
        />
        {errors.age && <span className="error-message">{errors.age}</span>}
      </div>
      
      <div className="form-group">
        <label>Country:</label>
        <select
          name="country"
          value={formData.country}
          onChange={handleChange}
        >
          <option value="">Select a country</option>
          <option value="us">United States</option>
          <option value="ca">Canada</option>
          <option value="uk">United Kingdom</option>
          <option value="au">Australia</option>
        </select>
      </div>
      
      <div className="form-group">
        <label>Interests:</label>
        {['Technology', 'Sports', 'Music', 'Travel', 'Reading'].map(interest => (
          <label key={interest} className="checkbox-label">
            <input
              type="checkbox"
              name="interests"
              value={interest}
              checked={formData.interests.includes(interest)}
              onChange={handleChange}
            />
            {interest}
          </label>
        ))}
      </div>
      
      <div className="form-group">
        <label className="checkbox-label">
          <input
            type="checkbox"
            name="newsletter"
            checked={formData.newsletter}
            onChange={handleChange}
          />
          Subscribe to newsletter
        </label>
      </div>
      
      <button 
        type="submit" 
        disabled={isSubmitting}
        className="submit-button"
      >
        {isSubmitting ? 'Registering...' : 'Register'}
      </button>
    </form>
  );
}
```
</details>
<details>
<summary><strong>19. What's the difference between controlled and uncontrolled components?</strong></summary>

**Answer:**
Controlled components have their form data handled by React state, making React the single source of truth. Uncontrolled components store their data in the DOM itself, and you access values using refs. Controlled components are preferred for most use cases.[^2]

**Example:**
```jsx
import React, { useState, useRef } from 'react';

// Controlled Component
function ControlledForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [message, setMessage] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Controlled Form Data:', { email, password, message });
    // React controls the data
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <h3>Controlled Form</h3>
      
      <div>
        <label>Email:</label>
        <input
          type="email"
          value={email} // Controlled by React state
          onChange={(e) => setEmail(e.target.value)}
          placeholder="Enter email"
        />
        <small>Current value: {email}</small>
      </div>
      
      <div>
        <label>Password:</label>
        <input
          type="password"
          value={password} // Controlled by React state
          onChange={(e) => setPassword(e.target.value)}
          placeholder="Enter password"
        />
      </div>
      
      <div>
        <label>Message:</label>
        <textarea
          value={message} // Controlled by React state
          onChange={(e) => setMessage(e.target.value)}
          placeholder="Enter message"
          rows="3"
        />
        <small>Characters: {message.length}</small>
      </div>
      
      <button type="submit">Submit Controlled</button>
    </form>
  );
}

// Uncontrolled Component
function UncontrolledForm() {
  const emailRef = useRef(null);
  const passwordRef = useRef(null);
  const messageRef = useRef(null);
  
  const handleSubmit = (e) => {
    e.preventDefault();
    // Access DOM values directly
    const formData = {
      email: emailRef.current.value,
      password: passwordRef.current.value,
      message: messageRef.current.value
    };
    console.log('Uncontrolled Form Data:', formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <h3>Uncontrolled Form</h3>
      
      <div>
        <label>Email:</label>
        <input
          type="email"
          ref={emailRef} // Access via ref
          defaultValue="" // Use defaultValue, not value
          placeholder="Enter email"
        />
      </div>
      
      <div>
        <label>Password:</label>
        <input
          type="password"
          ref={passwordRef} // Access via ref
          defaultValue=""
          placeholder="Enter password"
        />
      </div>
      
      <div>
        <label>Message:</label>
        <textarea
          ref={messageRef} // Access via ref
          defaultValue=""
          placeholder="Enter message"
          rows="3"
        />
      </div>
      
      <button type="submit">Submit Uncontrolled</button>
    </form>
  );
}

// Hybrid approach - File input (always uncontrolled)
function FileUploadForm() {
  const [fileName, setFileName] = useState('');
  const [preview, setPreview] = useState(null);
  const fileRef = useRef(null);
  
  const handleFileChange = (e) => {
    const file = e.target.files[^0];
    if (file) {
      setFileName(file.name);
      
      // Create preview for images
      if (file.type.startsWith('image/')) {
        const reader = new FileReader();
        reader.onload = (e) => setPreview(e.target.result);
        reader.readAsDataURL(file);
      }
    }
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    const file = fileRef.current.files[^0];
    if (file) {
      console.log('Selected file:', file);
      // Process file upload
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <h3>File Upload (Uncontrolled)</h3>
      
      <div>
        <label>Select File:</label>
        <input
          type="file"
          ref={fileRef}
          onChange={handleFileChange}
          accept="image/*"
        />
        {fileName && <small>Selected: {fileName}</small>}
      </div>
      
      {preview && (
        <div>
          <h4>Preview:</h4>
          <img 
            src={preview} 
            alt="Preview" 
            style={{ maxWidth: '200px', maxHeight: '200px' }}
          />
        </div>
      )}
      
      <button type="submit">Upload File</button>
    </form>
  );
}

// Main component
function FormExamples() {
  return (
    <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '20px' }}>
      <ControlledForm />
      <UncontrolledForm />
      <FileUploadForm />
    </div>
  );
}
```
</details>
<details>
<summary><strong>20. How do you optimize React component re-renders?</strong></summary>

**Answer:**
React component re-renders can be optimized using React.memo(), useMemo(), useCallback(), and proper component structure. The goal is to prevent unnecessary re-renders and expensive calculations when dependencies haven't changed.[^5]

**Example:**
```jsx
import React, { useState, useCallback, useMemo, memo } from 'react';

// Expensive calculation function
const expensiveCalculation = (items) => {
  console.log('Performing expensive calculation...');
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
};

// Memoized child component
const ProductItem = memo(({ product, onUpdate, onDelete }) => {
  console.log(`Rendering ProductItem: ${product.name}`);
  
  return (
    <div className="product-item">
      <h3>{product.name}</h3>
      <p>Price: ${product.price}</p>
      <p>Quantity: {product.quantity}</p>
      <button onClick={() => onUpdate(product.id, product.quantity + 1)}>
        Increase Quantity
      </button>
      <button onClick={() => onDelete(product.id)}>
        Delete
      </button>
    </div>
  );
});

// Optimized parent component
function ShoppingCart() {
  const [products, setProducts] = useState([
    { id: 1, name: 'Laptop', price: 999, quantity: 1 },
    { id: 2, name: 'Mouse', price: 29, quantity: 2 },
    { id: 3, name: 'Keyboard', price: 79, quantity: 1 }
  ]);
  
  const [searchTerm, setSearchTerm] = useState('');
  const [sortBy, setSortBy] = useState('name');
  
  // useCallback to memoize event handlers
  const handleUpdateQuantity = useCallback((productId, newQuantity) => {
    setProducts(prevProducts =>
      prevProducts.map(product =>
        product.id === productId
          ? { ...product, quantity: newQuantity }
          : product
      )
    );
  }, []);
  
  const handleDeleteProduct = useCallback((productId) => {
    setProducts(prevProducts =>
      prevProducts.filter(product => product.id !== productId)
    );
  }, []);
  
  // useMemo for expensive calculations
  const totalPrice = useMemo(() => {
    return expensiveCalculation(products);
  }, [products]);
  
  // useMemo for filtered and sorted products
  const processedProducts = useMemo(() => {
    console.log('Processing products...');
    
    let filtered = products.filter(product =>
      product.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
    
    filtered.sort((a, b) => {
      switch (sortBy) {
        case 'name':
          return a.name.localeCompare(b.name);
        case 'price':
          return a.price - b.price;
        case 'quantity':
          return a.quantity - b.quantity;
        default:
          return 0;
      }
    });
    
    return filtered;
  }, [products, searchTerm, sortBy]);
  
  return (
    <div className="shopping-cart">
      <h1>Shopping Cart</h1>
      
      <div className="controls">
        <input
          type="text"
          placeholder="Search products..."
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
        />
        
        <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
          <option value="name">Sort by Name</option>
          <option value="price">Sort by Price</option>
          <option value="quantity">Sort by Quantity</option>
        </select>
      </div>
      
      <div className="total">
        <h2>Total: ${totalPrice}</h2>
      </div>
      
      <div className="products">
        {processedProducts.map(product => (
          <ProductItem
            key={product.id}
            product={product}
            onUpdate={handleUpdateQuantity}
            onDelete={handleDeleteProduct}
          />
        ))}
      </div>
    </div>
  );
}

// Example of React.memo with custom comparison
const ExpensiveComponent = memo(({ data, config }) => {
  console.log('Rendering ExpensiveComponent');
  
  return (
    <div>
      <h3>Expensive Component</h3>
      <p>Processing {data.length} items...</p>
    </div>
  );
}, (prevProps, nextProps) => {
  // Custom comparison function
  return (
    prevProps.data.length === nextProps.data.length &&
    prevProps.config.setting === nextProps.config.setting
  );
});

// Performance monitoring component
function PerformanceDemo() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState(Array.from({ length: 1000 }, (_, i) => ({ id: i, value: i })));
  
  // This will cause re-render every time (bad)
  const handleBadClick = () => {
    console.log('Bad handler called');
  };
  
  // This won't cause re-render (good)
  const handleGoodClick = useCallback(() => {
    console.log('Good handler called');
  }, []);
  
  // Expensive calculation without memoization (bad)
  const expensiveValue = items.reduce((sum, item) => sum + item.value, 0);
  
  // Expensive calculation with memoization (good)
  const memoizedExpensiveValue = useMemo(() => {
    console.log('Calculating expensive value...');
    return items.reduce((sum, item) => sum + item.value, 0);
  }, [items]);
  
  return (
    <div>
      <h2>Performance Demo</h2>
      <p>Count: {count}</p>
      <p>Expensive Value: {memoizedExpensiveValue}</p>
      
      <button onClick={() => setCount(count + 1)}>
        Increment Count
      </button>
      
      <ExpensiveComponent 
        data={items}
        config={{ setting: 'default' }}
      />
      
      <ChildComponent
        onClick={handleGoodClick} // Memoized
        data={{ count }} // This will cause re-render when count changes
      />
    </div>
  );
}

const ChildComponent = memo(({ onClick, data }) => {
  console.log('ChildComponent rendered');
  return (
    <div>
      <p>Child component with count: {data.count}</p>
      <button onClick={onClick}>Click me</button>
    </div>
  );
});
```
</details>

***

## State and Props 📊

<details>
<summary><strong>21. What is state in React?</strong></summary>

**Answer:**
State is a built-in object that stores property values that belong to a component. State is mutable and when it changes, the component re-renders. State should only be modified using the setState() method in class components or state setters in functional components.[^4][^2]

**Example:**
```jsx
import React, { useState } from 'react';

// Class component state
class ClassCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      message: 'Hello World',
      user: {
        name: 'John',
        email: 'john@example.com'
      }
    };
  }
  
  incrementCount = () => {
    this.setState(prevState => ({
      count: prevState.count + 1
    }));
  };
  
  updateUser = () => {
    this.setState(prevState => ({
      user: {
        ...prevState.user,
        name: 'Jane'
      }
    }));
  };
  
  render() {
    return (
      <div>
        <h2>Class Component State</h2>
        <p>Count: {this.state.count}</p>
        <p>Message: {this.state.message}</p>
        <p>User: {this.state.user.name} ({this.state.user.email})</p>
        <button onClick={this.incrementCount}>Increment</button>
        <button onClick={this.updateUser}>Update User</button>
      </div>
    );
  }
}

// Functional component state with hooks
function FunctionalCounter() {
  const [count, setCount] = useState(0);
  const [message, setMessage] = useState('Hello World');
  const [user, setUser] = useState({
    name: 'John',
    email: 'john@example.com'
  });
  const [todos, setTodos] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  
  const incrementCount = () => {
    setCount(prevCount => prevCount + 1);
    // or simply: setCount(count + 1);
  };
  
  const addTodo = () => {
    const newTodo = {
      id: Date.now(),
      text: `Todo ${todos.length + 1}`,
      completed: false
    };
    setTodos(prevTodos => [...prevTodos, newTodo]);
  };
  
  const toggleTodo = (id) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };
  
  const updateUserName = () => {
    setUser(prevUser => ({
      ...prevUser,
      name: prevUser.name === 'John' ? 'Jane' : 'John'
    }));
  };
  
  const fetchData = async () => {
    setIsLoading(true);
    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 2000));
      setMessage('Data loaded successfully!');
    } catch (error) {
      setMessage('Error loading data');
    } finally {
      setIsLoading(false);
    }
  };
  
  return (
    <div>
      <h2>Functional Component State</h2>
      
      <div>
        <h3>Counter</h3>
        <p>Count: {count}</p>
        <button onClick={incrementCount}>Increment</button>
      </div>
      
      <div>
        <h3>Message</h3>
        <p>Message: {message}</p>
        <button onClick={fetchData} disabled={isLoading}>
          {isLoading ? 'Loading...' : 'Fetch Data'}
        </button>
      </div>
      
      <div>
        <h3>User</h3>
        <p>User: {user.name} ({user.email})</p>
        <button onClick={updateUserName}>Toggle Name</button>
      </div>
      
      <div>
        <h3>Todo List</h3>
        <button onClick={addTodo}>Add Todo</button>
        <ul>
          {todos.map(todo => (
            <li 
              key={todo.id}
              onClick={() => toggleTodo(todo.id)}
              style={{ 
                textDecoration: todo.completed ? 'line-through' : 'none',
                cursor: 'pointer'
              }}
            >
              {todo.text}
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
}

// Complex state management example
function UserProfile() {
  const [profile, setProfile] = useState({
    personal: {
      firstName: '',
      lastName: '',
      email: '',
      phone: ''
    },
    preferences: {
      theme: 'light',
      notifications: true,
      language: 'en'
    },
    settings: {
      privacy: 'public',
      newsletter: false
    }
  });
  
  // Update nested state
  const updatePersonal = (field, value) => {
    setProfile(prevProfile => ({
      ...prevProfile,
      personal: {
        ...prevProfile.personal,
        [field]: value
      }
    }));
  };
  
  const updatePreferences = (field, value) => {
    setProfile(prevProfile => ({
      ...prevProfile,
      preferences: {
        ...prevProfile.preferences,
        [field]: value
      }
    }));
  };
  
  return (
    <div>
      <h3>User Profile</h3>
      
      <div>
        <h4>Personal Info</h4>
        <input
          placeholder="First Name"
          value={profile.personal.firstName}
          onChange={(e) => updatePersonal('firstName', e.target.value)}
        />
        <input
          placeholder="Last Name"
          value={profile.personal.lastName}
          onChange={(e) => updatePersonal('lastName', e.target.value)}
        />
        <input
          placeholder="Email"
          value={profile.personal.email}
          onChange={(e) => updatePersonal('email', e.target.value)}
        />
      </div>
      
      <div>
        <h4>Preferences</h4>
        <label>
          Theme:
          <select
            value={profile.preferences.theme}
            onChange={(e) => updatePreferences('theme', e.target.value)}
          >
            <option value="light">Light</option>
            <option value="dark">Dark</option>
          </select>
        </label>
        
        <label>
          <input
            type="checkbox"
            checked={profile.preferences.notifications}
            onChange={(e) => updatePreferences('notifications', e.target.checked)}
          />
          Enable Notifications
        </label>
      </div>
      
      <div>
        <h4>Current Profile:</h4>
        <pre>{JSON.stringify(profile, null, 2)}</pre>
      </div>
    </div>
  );
}

export default function StateExamples() {
  return (
    <div>
      <ClassCounter />
      <hr />
      <FunctionalCounter />
      <hr />
      <UserProfile />
    </div>
  );
}
```
</details>
<details>
<summary><strong>22. What's the difference between props and state?</strong></summary>

**Answer:**
Props are read-only data passed from parent to child components, while state is mutable data managed within a component. Props flow down the component tree, while state is local to the component. Changes to props or state trigger re-renders.[^4][^2]

**Example:**
```jsx
import React, { useState } from 'react';

// Child component receiving props
function UserCard({ user, isSelected, onSelect, onEdit, theme }) {
  // Props are read-only - you cannot do this:
  // user.name = "New Name"; // ❌ This would be wrong!
  
  return (
    <div 
      className={`user-card ${theme} ${isSelected ? 'selected' : ''}`}
      onClick={() => onSelect(user.id)}
    >
      <img src={user.avatar} alt={user.name} />
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      <p>Role: {user.role}</p>
      <button onClick={() => onEdit(user)}>Edit</button>
      
      {/* Props can be any type */}
      <small>Theme from props: {theme}</small>
    </div>
  );
}

// Parent component managing state and passing props
function UserManagement() {
  // State - managed within this component
  const [users, setUsers] = useState([
    {
      id: 1,
      name: 'John Doe',
      email: 'john@example.com',
      role: 'Admin',
      avatar: '/avatars/john.jpg'
    },
    {
      id: 2,
      name: 'Jane Smith',
      email: 'jane@example.com',
      role: 'User',
      avatar: '/avatars/jane.jpg'
    }
  ]);
  
  const [selectedUserId, setSelectedUserId] = useState(null);
  const [theme, setTheme] = useState('light');
  const [editingUser, setEditingUser] = useState(null);
  
  // State can be modified with setter functions
  const handleSelectUser = (userId) => {
    setSelectedUserId(userId);
  };
  
  const handleEditUser = (user) => {
    setEditingUser(user);
  };
  
  const handleSaveUser = (updatedUser) => {
    setUsers(prevUsers =>
      prevUsers.map(user =>
        user.id === updatedUser.id ? updatedUser : user
      )
    );
    setEditingUser(null);
  };
  
  const toggleTheme = () => {
    setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
  };
  
  return (
    <div className={`user-management ${theme}`}>
      <h1>User Management</h1>
      
      {/* State controls UI */}
      <div className="controls">
        <button onClick={toggleTheme}>
          Toggle Theme (current: {theme})
        </button>
        <p>Selected User ID: {selectedUserId || 'None'}</p>
      </div>
      
      {/* Passing state as props to children */}
      <div className="user-list">
        {users.map(user => (
          <UserCard
            key={user.id}
            user={user}                    // Props: user data
            isSelected={selectedUserId === user.id} // Props: computed from state
            onSelect={handleSelectUser}    // Props: callback function
            onEdit={handleEditUser}        // Props: callback function
            theme={theme}                  // Props: theme state
          />
        ))}
      </div>
      
      {/* Conditional rendering based on state */}
      {editingUser && (
        <EditUserModal
          user={editingUser}              // Props: user being edited
          onSave={handleSaveUser}         // Props: save callback
          onCancel={() => setEditingUser(null)} // Props: cancel callback
        />
      )}
    </div>
  );
}

// Modal component with internal state and external props
function EditUserModal({ user, onSave, onCancel }) {
  // Internal state for form data
  const [formData, setFormData] = useState({
    name: user.name,
    email: user.email,
    role: user.role
  });
  
  const [errors, setErrors] = useState({});
  
  const handleChange = (field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
    
    // Clear error when user starts typing
    if (errors[field]) {
      setErrors(prev => ({
        ...prev,
        [field]: ''
      }));
    }
  };
  
  const validate = () => {
    const newErrors = {};
    if (!formData.name.trim()) newErrors.name = 'Name is required';
    if (!formData.email.trim()) newErrors.email = 'Email is required';
    if (!formData.role.trim()) newErrors.role = 'Role is required';
    return newErrors;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    
    const newErrors = validate();
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }
    
    // Pass updated data back to parent via props callback
    onSave({
      ...user,
      ...formData
    });
  };
  
  return (
    <div className="modal-backdrop">
      <div className="modal">
        <h2>Edit User: {user.name}</h2>
        
        <form onSubmit={handleSubmit}>
          <div>
            <label>Name:</label>
            <input
              type="text"
              value={formData.name} // Controlled by local state
              onChange={(e) => handleChange('name', e.target.value)}
            />
            {errors.name && <span className="error">{errors.name}</span>}
          </div>
          
          <div>
            <label>Email:</label>
            <input
              type="email"
              value={formData.email} // Controlled by local state
              onChange={(e) => handleChange('email', e.target.value)}
            />
            {errors.email && <span className="error">{errors.email}</span>}
          </div>
          
          <div>
            <label>Role:</label>
            <select
              value={formData.role} // Controlled by local state
              onChange={(e) => handleChange('role', e.target.value)}
            >
              <option value="">Select Role</option>
              <option value="Admin">Admin</option>
              <option value="User">User</option>
              <option value="Manager">Manager</option>
            </select>
            {errors.role && <span className="error">{errors.role}</span>}
          </div>
          
          <div className="modal-actions">
            <button type="submit">Save</button>
            <button type="button" onClick={onCancel}>Cancel</button>
          </div>
        </form>
      </div>
    </div>
  );
}

// Comparison component showing props vs state
function PropsVsStateDemo() {
  return (
    <div>
      <h2>Props vs State Comparison</h2>
      
      <table>
        <thead>
          <tr>
            <th>Aspect</th>
            <th>Props</th>
            <th>State</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Mutability</td>
            <td>Read-only (immutable)</td>
            <td>Mutable</td>
          </tr>
          <tr>
            <td>Source</td>
            <td>Passed from parent</td>
            <td>Managed within component</td>
          </tr>
          <tr>
            <td>Purpose</td>
            <td>Configure component</td>
            <td>Manage component data</td>
          </tr>
          <tr>
            <td>Updates</td>
            <td>Parent controls updates</td>
            <td>Component controls updates</td>
          </tr>
          <tr>
            <td>Initial Values</td>
            <td>Set by parent</td>
            <td>Set in component</td>
          </tr>
        </tbody>
      </table>
      
      <UserManagement />
    </div>
  );
}

export default PropsVsStateDemo;
```
</details>
<details>
<summary><strong>23. How do you manage state in a large React application?</strong></summary>

**Answer:**
Large React applications require structured state management approaches. You can use local component state for UI state, Context API for global state, and libraries like Redux, Zustand, or Recoil for complex application state. The key is choosing the right tool for each use case.[^5]

**Example:**
```jsx
import React, { createContext, useContext, useReducer, useState } from 'react';

// 1. Context API for Global State
const AppContext = createContext();

// State structure for large app
const initialState = {
  user: null,
  theme: 'light',
  notifications: [],
  cart: {
    items: [],
    total: 0
  },
  ui: {
    loading: false,
    sidebarOpen: false,
    modal: null
  }
};

// Reducer for complex state updates
function appReducer(state, action) {
  switch (action.type) {
    case 'SET_USER':
      return { ...state, user: action.payload };
    
    case 'SET_THEME':
      return { ...state, theme: action.payload };
    
    case 'ADD_NOTIFICATION':
      return {
        ...state,
        notifications: [...state.notifications, action.payload]
      };
    
    case 'REMOVE_NOTIFICATION':
      return {
        ...state,
        notifications: state.notifications.filter(n => n.id !== action.payload)
      };
    
    case 'ADD_TO_CART':
      const existingItem = state.cart.items.find(item => item.id === action.payload.id);
      let updatedItems;
      
      if (existingItem) {
        updatedItems = state.cart.items.map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      } else {
        updatedItems = [...state.cart.items, { ...action.payload, quantity: 1 }];
      }
      
      const total = updatedItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);
      
      return {
        ...state,
        cart: { items: updatedItems, total }
      };
    
    case 'REMOVE_FROM_CART':
      const filteredItems = state.cart.items.filter(item => item.id !== action.payload);
      const newTotal = filteredItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);
      
      return {
        ...state,
        cart: { items: filteredItems, total: newTotal }
      };
    
    case 'SET_LOADING':
      return {
        ...state,
        ui: { ...state.ui, loading: action.payload }
      };
    
    case 'TOGGLE_SIDEBAR':
      return {
        ...state,
        ui: { ...state.ui, sidebarOpen: !state.ui.sidebarOpen }
      };
    
    case 'SHOW_MODAL':
      return {
        ...state,
        ui: { ...state.ui, modal: action.payload }
      };
    
    case 'HIDE_MODAL':
      return {
        ...state,
        ui: { ...state.ui, modal: null }
      };
    
    default:
      return state;
  }
}

// Context Provider
function AppProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, initialState);
  
  // Actions
  const actions = {
    login: (user) => {
      dispatch({ type: 'SET_USER', payload: user });
      dispatch({
        type: 'ADD_NOTIFICATION',
        payload: {
          id: Date.now(),
          type: 'success',
          message: `Welcome back, ${user.name}!`
        }
      });
    },
    
    logout: () => {
      dispatch({ type: 'SET_USER', payload: null });
      dispatch({
        type: 'ADD_NOTIFICATION',
        payload: {
          id: Date.now(),
          type: 'info',
          message: 'You have been logged out'
        }
      });
    },
    
    setTheme: (theme) => {
      dispatch({ type: 'SET_THEME', payload: theme });
      localStorage.setItem('theme', theme);
    },
    
    addToCart: (product) => {
      dispatch({ type: 'ADD_TO_CART', payload: product });
      dispatch({
        type: 'ADD_NOTIFICATION',
        payload: {
          id: Date.now(),
          type: 'success',
          message: `${product.name} added to cart`
        }
      });
    },
    
    removeFromCart: (productId) => {
      dispatch({ type: 'REMOVE_FROM_CART', payload: productId });
    },
    
    showModal: (modalType, data = null) => {
      dispatch({ type: 'SHOW_MODAL', payload: { type: modalType, data } });
    },
    
    hideModal: () => {
      dispatch({ type: 'HIDE_MODAL' });
    },
    
    toggleSidebar: () => {
      dispatch({ type: 'TOGGLE_SIDEBAR' });
    }
  };
  
  return (
    <AppContext.Provider value={{ state, actions }}>
      {children}
    </AppContext.Provider>
  );
}

// Custom hook for using app context
function useAppContext() {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useAppContext must be used within AppProvider');
  }
  return context;
}

// 2. Custom hooks for specific state domains
function useAuth() {
  const { state, actions } = useAppContext();
  
  return {
    user: state.user,
    isAuthenticated: !!state.user,
    login: actions.login,
    logout: actions.logout
  };
}

function useCart() {
  const { state, actions } = useAppContext();
  
  return {
    cart: state.cart,
    addToCart: actions.addToCart,
    removeFromCart: actions.removeFromCart,
    itemCount: state.cart.items.length,
    isEmpty: state.cart.items.length === 0
  };
}

function useUI() {
  const { state, actions } = useAppContext();
  
  return {
    loading: state.ui.loading,
    sidebarOpen: state.ui.sidebarOpen,
    modal: state.ui.modal,
    theme: state.theme,
    toggleSidebar: actions.toggleSidebar,
    showModal: actions.showModal,
    hideModal: actions.hideModal,
    setTheme: actions.setTheme
  };
}

// 3. Component-level state for local UI
function ProductCard({ product }) {
  // Local state for component-specific UI
  const [isHovered, setIsHovered] = useState(false);
  const [imageLoaded, setImageLoaded] = useState(false);
  
  // Global state through custom hooks
  const { addToCart } = useCart();
  const { showModal } = useUI();
  
  return (
    <div 
      className={`product-card ${isHovered ? 'hovered' : ''}`}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <img
        src={product.image}
        alt={product.name}
        onLoad={() => setImageLoaded(true)}
        style={{ opacity: imageLoaded ? 1 : 0.5 }}
      />
      
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      
      <div className="actions">
        <button onClick={() => addToCart(product)}>
          Add to Cart
        </button>
        <button onClick={() => showModal('product-details', product)}>
          View Details
        </button>
      </div>
    </div>
  );
}

// Header component using global state
function Header() {
  const { user, logout } = useAuth();
  const { cart } = useCart();
  const { toggleSidebar, theme, setTheme } = useUI();
  
  return (
    <header className={`header ${theme}`}>
      <div className="header-left">
        <button onClick={toggleSidebar}>☰ Menu</button>
        <h1>My Store</h1>
      </div>
      
      <div className="header-right">
        <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
          {theme === 'light' ? '🌙' : '☀️'}
        </button>
        
        <div className="cart-icon">
          🛒 <span className="cart-count">{cart.items.length}</span>
        </div>
        
        {user ? (
          <div className="user-menu">
            <span>Hello, {user.name}</span>
            <button onClick={logout}>Logout</button>
          </div>
        ) : (
          <button>Login</button>
        )}
      </div>
    </header>
  );
}

// Notification system using global state
function NotificationCenter() {
  const { state } = useAppContext();
  
  return (
    <div className="notification-center">
      {state.notifications.map(notification => (
        <div 
          key={notification.id}
          className={`notification ${notification.type}`}
        >
          {notification.message}
        </div>
      ))}
    </div>
  );
}

// Main App component
function App() {
  return (
    <AppProvider>
      <div className="app">
        <Header />
        <NotificationCenter />
        
        {/* Your app content */}
        <main>
          <div className="products">
            {/* Product list would go here */}
          </div>
        </main>
      </div>
    </AppProvider>
  );
}

// 4. Alternative: Using a custom state management solution
class StateManager {
  constructor() {
    this.state = {};
    this.listeners = [];
  }
  
  getState() {
    return this.state;
  }
  
  setState(newState) {
    this.state = { ...this.state, ...newState };
    this.listeners.forEach(listener => listener(this.state));
  }
  
  subscribe(listener) {
    this.listeners.push(listener);
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }
}

// Usage with custom state manager
const globalState = new StateManager();

function useGlobalState() {
  const [state, setState] = useState(globalState.getState());
  
  React.useEffect(() => {
    return globalState.subscribe(setState);
  }, []);
  
  return [state, (newState) => globalState.setState(newState)];
}

export default App;
```
</details>
<details>
<summary><strong>24. What is lifting state up in React?</strong></summary>

**Answer:**
Lifting state up means moving state from child components to their closest common ancestor. This pattern enables sharing state between sibling components and helps maintain a single source of truth for shared data.[^4]

**Example:**
```jsx
import React, { useState } from 'react';

// Before lifting state up - each component has its own state
function BadExample() {
  return (
    <div>
      <h2>❌ Bad Example - Separate States</h2>
      <TemperatureInput scale="c" />
      <TemperatureInput scale="f" />
      <p>These components don't sync with each other!</p>
    </div>
  );
}

function TemperatureInput({ scale }) {
  const [temperature, setTemperature] = useState('');
  
  const scaleName = scale === 'c' ? 'Celsius' : 'Fahrenheit';
  
  return (
    <fieldset>
      <legend>Enter temperature in {scaleName}:</legend>
      <input
        value={temperature}
        onChange={(e) => setTemperature(e.target.value)}
      />
    </fieldset>
  );
}

// After lifting state up - shared state in parent
function GoodExample() {
  const [temperature, setTemperature] = useState('');
  const [scale, setScale] = useState('c');
  
  const handleCelsiusChange = (temperature) => {
    setScale('c');
    setTemperature(temperature);
  };
  
  const handleFahrenheitChange = (temperature) => {
    setScale('f');
    setTemperature(temperature);
  };
  
  const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
  const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;
  
  return (
    <div>
      <h2>✅ Good Example - Lifted State</h2>
      <TemperatureInputControlled
        scale="c"
        temperature={celsius}
        onTemperatureChange={handleCelsiusChange}
      />
      <TemperatureInputControlled
        scale="f"
        temperature={fahrenheit}
        onTemperatureChange={handleFahrenheitChange}
      />
      <BoilingVerdict celsius={parseFloat(celsius)} />
    </div>
  );
}

function TemperatureInputControlled({ scale, temperature, onTemperatureChange }) {
  const scaleName = scale === 'c' ? 'Celsius' : 'Fahrenheit';
  
  return (
    <fieldset>
      <legend>Enter temperature in {scaleName}:</legend>
      <input
        value={temperature}
        onChange={(e) => onTemperatureChange(e.target.value)}
      />
    </fieldset>
  );
}

function BoilingVerdict({ celsius }) {
  if (isNaN(celsius)) {
    return <p>Please enter a valid temperature.</p>;
  }
  
  if (celsius >= 100) {
    return <p>The water would boil.</p>;
  }
  
  return <p>The water would not boil.</p>;
}

// Conversion functions
function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}

function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) {
    return '';
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}

// More complex example: Shopping cart
function ShoppingApp() {
  // Lifted state - shared between multiple components
  const [cartItems, setCartItems] = useState([]);
  const [selectedCategory, setSelectedCategory] = useState('all');
  const [searchQuery, setSearchQuery] = useState('');
  const [user, setUser] = useState(null);
  
  // Products data
  const [products] = useState([
    { id: 1, name: 'Laptop', price: 999, category: 'electronics' },
    { id: 2, name: 'Phone', price: 599, category: 'electronics' },
    { id: 3, name: 'Shirt', price: 29, category: 'clothing' },
    { id: 4, name: 'Shoes', price: 89, category: 'clothing' },
    { id: 5, name: 'Book', price: 19, category: 'books' }
  ]);
  
  // Shared actions
  const addToCart = (product) => {
    setCartItems(prevItems => {
      const existingItem = prevItems.find(item => item.id === product.id);
      if (existingItem) {
        return prevItems.map(item =>
          item.id === product.id 
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      }
      return [...prevItems, { ...product, quantity: 1 }];
    });
  };
  
  const removeFromCart = (productId) => {
    setCartItems(prevItems => prevItems.filter(item => item.id !== productId));
  };
  
  const updateQuantity = (productId, quantity) => {
    if (quantity <= 0) {
      removeFromCart(productId);
      return;
    }
    
    setCartItems(prevItems =>
      prevItems.map(item =>
        item.id === productId 
          ? { ...item, quantity }
          : item
      )
    );
  };
  
  // Filtered products based on lifted state
  const filteredProducts = products.filter(product => {
    const matchesCategory = selectedCategory === 'all' || product.category === selectedCategory;
    const matchesSearch = product.name.toLowerCase().includes(searchQuery.toLowerCase());
    return matchesCategory && matchesSearch;
  });
  
  const totalPrice = cartItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);
  
  return (
    <div className="shopping-app">
      <Header
        user={user}
        cartItemsCount={cartItems.length}
        onLogin={setUser}
        onLogout={() => setUser(null)}
      />
      
      <div className="main-content">
        <Sidebar
          selectedCategory={selectedCategory}
          onCategoryChange={setSelectedCategory}
        />
        
        <div className="content">
          <SearchBar
            searchQuery={searchQuery}
            onSearchChange={setSearchQuery}
          />
          
          <ProductList
            products={filteredProducts}
            onAddToCart={addToCart}
          />
        </div>
        
        <Cart
          items={cartItems}
          total={totalPrice}
          onUpdateQuantity={updateQuantity}
          onRemoveItem={removeFromCart}
        />
      </div>
    </div>
  );
}

// Child components receive shared state as props
function Header({ user, cartItemsCount, onLogin, onLogout }) {
  return (
    <header className="header">
      <h1>My Shop</h1>
      
      <div className="header-right">
        <div className="cart-icon">
          🛒 {cartItemsCount > 0 && <span className="badge">{cartItemsCount}</span>}
        </div>
        
        {user ? (
          <div className="user-menu">
            <span>Hello, {user.name}</span>
            <button onClick={onLogout}>Logout</button>
          </div>
        ) : (
          <button onClick={() => onLogin({ id: 1, name: 'John Doe' })}>
            Login
          </button>
        )}
      </div>
    </header>
  );
}

function Sidebar({ selectedCategory, onCategoryChange }) {
  const categories = [
    { id: 'all', name: 'All Products' },
    { id: 'electronics', name: 'Electronics' },
    { id: 'clothing', name: 'Clothing' },
    { id: 'books', name: 'Books' }
  ];
  
  return (
    <aside className="sidebar">
      <h3>Categories</h3>
      <ul>
        {categories.map(category => (
          <li key={category.id}>
            <button
              className={selectedCategory === category.id ? 'active' : ''}
              onClick={() => onCategoryChange(category.id)}
            >
              {category.name}
            </button>
          </li>
        ))}
      </ul>
    </aside>
  );
}

function SearchBar({ searchQuery, onSearchChange }) {
  return (
    <div className="search-bar">
      <input
        type="text"
        placeholder="Search products..."
        value={searchQuery}
        onChange={(e) => onSearchChange(e.target.value)}
      />
    </div>
  );
}

function ProductList({ products, onAddToCart }) {
  return (
    <div className="product-list">
      <h2>Products ({products.length})</h2>
      <div className="products-grid">
        {products.map(product => (
          <div key={product.id} className="product-card">
            <h3>{product.name}</h3>
            <p>${product.price}</p>
            <button onClick={() => onAddToCart(product)}>
              Add to Cart
            </button>
          </div>
        ))}
      </div>
    </div>
  );
}

function Cart({ items, total, onUpdateQuantity, onRemoveItem }) {
  return (
    <aside className="cart">
      <h3>Shopping Cart</h3>
      
      {items.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <>
          {items.map(item => (
            <div key={item.id} className="cart-item">
              <h4>{item.name}</h4>
              <p>${item.price} each</p>
              
              <div className="quantity-controls">
                <button onClick={() => onUpdateQuantity(item.id, item.quantity - 1)}>
                  -
                </button>
                <span>{item.quantity}</span>
                <button onClick={() => onUpdateQuantity(item.id, item.quantity + 1)}>
                  +
                </button>
              </div>
              
              <p>Subtotal: ${item.price * item.quantity}</p>
              <button onClick={() => onRemoveItem(item.id)}>Remove</button>
            </div>
          ))}
          
          <div className="cart-total">
            <strong>Total: ${total}</strong>
          </div>
          
          <button className="checkout-btn">Checkout</button>
        </>
      )}
    </div>
  );
}

// Main component demonstrating both examples
export default function LiftingStateExample() {
  return (
    <div>
      <BadExample />
      <hr />
      <GoodExample />
      <hr />
      <ShoppingApp />
    </div>
  );
}
```
</details>
<details>
<summary><strong>25. What is prop drilling and how do you avoid it?</strong></summary>

**Answer:**
Prop drilling occurs when you pass data through multiple component layers that don't need the data, just to get it to a deeply nested component. You can avoid it using Context API, custom hooks, state management libraries, or component composition patterns.[^3]

**Example:**
```jsx
import React, { useState, createContext, useContext } from 'react';

// 🔴 PROBLEM: Prop Drilling Example
function PropDrillingExample() {
  const [user, setUser] = useState({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    preferences: {
      theme: 'dark',
      language: 'en',
      notifications: true
    }
  });
  
  const [cartItems, setCartItems] = useState([
    { id: 1, name: 'Laptop', quantity: 1, price: 999 }
  ]);
  
  // Props are drilled through multiple levels
  return (
    <div>
      <h2>🔴 Prop Drilling Problem</h2>
      <Layout 
        user={user} 
        cartItems={cartItems}
        onUpdateUser={setUser}
        onUpdateCart={setCartItems}
      />
    </div>
  );
}

// Layout doesn't use these props, just passes them down
function Layout({ user, cartItems, onUpdateUser, onUpdateCart }) {
  return (
    <div className="layout">
      <Header user={user} cartItems={cartItems} />
      <MainContent 
        user={user} 
        cartItems={cartItems}
        onUpdateUser={onUpdateUser}
        onUpdateCart={onUpdateCart}
      />
      <Footer user={user} />
    </div>
  );
}

// Header doesn't use all props, just passes some down
function Header({ user, cartItems }) {
  return (
    <header>
      <h1>My App</h1>
      <UserInfo user={user} />
      <CartIcon cartItems={cartItems} />
    </header>
  );
}

// MainContent doesn't use props directly, passes them down
function MainContent({ user, cartItems, onUpdateUser, onUpdateCart }) {
  return (
    <main>
      <UserDashboard 
        user={user}
        onUpdateUser={onUpdateUser}
      />
      <ProductSection 
        cartItems={cartItems}
        onUpdateCart={onUpdateCart}
      />
    </main>
  );
}

// These components finally use the props
function UserInfo({ user }) {
  return <span>Welcome, {user.name}!</span>;
}

function CartIcon({ cartItems }) {
  return <span>Cart ({cartItems.length})</span>;
}

function UserDashboard({ user, onUpdateUser }) {
  const toggleTheme = () => {
    onUpdateUser(prevUser => ({
      ...prevUser,
      preferences: {
        ...prevUser.preferences,
        theme: prevUser.preferences.theme === 'dark' ? 'light' : 'dark'
      }
    }));
  };
  
  return (
    <div>
      <h3>User Dashboard</h3>
      <p>Theme: {user.preferences.theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

function ProductSection({ cartItems, onUpdateCart }) {
  const addItem = () => {
    const newItem = {
      id: Date.now(),
      name: 'New Product',
      quantity: 1,
      price: 50
    };
    onUpdateCart(prev => [...prev, newItem]);
  };
  
  return (
    <div>
      <h3>Products</h3>
      <button onClick={addItem}>Add Product to Cart</button>
    </div>
  );
}

function Footer({ user }) {
  return <footer>© 2024 {user.name}'s App</footer>;
}

// 🟢 SOLUTION 1: Context API
const UserContext = createContext();
const CartContext = createContext();

function ContextSolution() {
  const [user, setUser] = useState({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    preferences: {
      theme: 'dark',
      language: 'en',
      notifications: true
    }
  });
  
  const [cartItems, setCartItems] = useState([
    { id: 1, name: 'Laptop', quantity: 1, price: 999 }
  ]);
  
  return (
    <div>
      <h2>🟢 Solution 1: Context API</h2>
      <UserContext.Provider value={{ user, setUser }}>
        <CartContext.Provider value={{ cartItems, setCartItems }}>
          <LayoutWithContext />
        </CartContext.Provider>
      </UserContext.Provider>
    </div>
  );
}

// No more prop drilling!
function LayoutWithContext() {
  return (
    <div className="layout">
      <HeaderWithContext />
      <MainContentWithContext />
      <FooterWithContext />
    </div>
  );
}

function HeaderWithContext() {
  return (
    <header>
      <h1>My App</h1>
      <UserInfoWithContext />
      <CartIconWithContext />
    </header>
  );
}

function MainContentWithContext() {
  return (
    <main>
      <UserDashboardWithContext />
      <ProductSectionWithContext />
    </main>
  );
}

// Components consume context directly
function UserInfoWithContext() {
  const { user } = useContext(UserContext);
  return <span>Welcome, {user.name}!</span>;
}

function CartIconWithContext() {
  const { cartItems } = useContext(CartContext);
  return <span>Cart ({cartItems.length})</span>;
}

function UserDashboardWithContext() {
  const { user, setUser } = useContext(UserContext);
  
  const toggleTheme = () => {
    setUser(prevUser => ({
      ...prevUser,
      preferences: {
        ...prevUser.preferences,
        theme: prevUser.preferences.theme === 'dark' ? 'light' : 'dark'
      }
    }));
  };
  
  return (
    <div>
      <h3>User Dashboard</h3>
      <p>Theme: {user.preferences.theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

function ProductSectionWithContext() {
  const { setCartItems } = useContext(CartContext);
  
  const addItem = () => {
    const newItem = {
      id: Date.now(),
      name: 'New Product',
      quantity: 1,
      price: 50
    };
    setCartItems(prev => [...prev, newItem]);
  };
  
  return (
    <div>
      <h3>Products</h3>
      <button onClick={addItem}>Add Product to Cart</button>
    </div>
  );
}

function FooterWithContext() {
  const { user } = useContext(UserContext);
  return <footer>© 2024 {user.name}'s App</footer>;
}

// 🟢 SOLUTION 2: Custom Hooks with Context
const AppContext = createContext();

function useAppContext() {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useAppContext must be used within AppProvider');
  }
  return context;
}

function useUser() {
  const { user, setUser } = useAppContext();
  
  const updateUserPreference = (key, value) => {
    setUser(prevUser => ({
      ...prevUser,
      preferences: {
        ...prevUser.preferences,
        [key]: value
      }
    }));
  };
  
  return { user, setUser, updateUserPreference };
}

function useCart() {
  const { cartItems, setCartItems } = useAppContext();
  
  const addToCart = (product) => {
    setCartItems(prev => [...prev, { ...product, id: Date.now() }]);
  };
  
  const removeFromCart = (id) => {
    setCartItems(prev => prev.filter(item => item.id !== id));
  };
  
  const totalItems = cartItems.length;
  const totalPrice = cartItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);
  
  return { 
    cartItems, 
    addToCart, 
    removeFromCart, 
    totalItems, 
    totalPrice 
  };
}

function CustomHooksSolution() {
  const [user, setUser] = useState({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    preferences: {
      theme: 'dark',
      language: 'en',
      notifications: true
    }
  });
  
  const [cartItems, setCartItems] = useState([
    { id: 1, name: 'Laptop', quantity: 1, price: 999 }
  ]);
  
  return (
    <div>
      <h2>🟢 Solution 2: Custom Hooks</h2>
      <AppContext.Provider value={{ user, setUser, cartItems, setCartItems }}>
        <LayoutWithHooks />
      </AppContext.Provider>
    </div>
  );
}

function LayoutWithHooks() {
  return (
    <div className="layout">
      <HeaderWithHooks />
      <MainContentWithHooks />
      <FooterWithHooks />
    </div>
  );
}

function HeaderWithHooks() {
  return (
    <header>
      <h1>My App</h1>
      <UserInfoWithHooks />
      <CartIconWithHooks />
    </header>
  );
}

function MainContentWithHooks() {
  return (
    <main>
      <UserDashboardWithHooks />
      <ProductSectionWithHooks />
    </main>
  );
}

function UserInfoWithHooks() {
  const { user } = useUser();
  return <span>Welcome, {user.name}!</span>;
}

function CartIconWithHooks() {
  const { totalItems } = useCart();
  return <span>Cart ({totalItems})</span>;
}

function UserDashboardWithHooks() {
  const { user, updateUserPreference } = useUser();
  
  return (
    <div>
      <h3>User Dashboard</h3>
      <p>Theme: {user.preferences.theme}</p>
      <button onClick={() => updateUserPreference('theme', 
        user.preferences.theme === 'dark' ? 'light' : 'dark'
      )}>
        Toggle Theme
      </button>
    </div>
  );
}

function ProductSectionWithHooks() {
  const { addToCart } = useCart();
  
  const handleAddProduct = () => {
    addToCart({
      name: 'New Product',
      quantity: 1,
      price: 50
    });
  };
  
  return (
    <div>
      <h3>Products</h3>
      <button onClick={handleAddProduct}>Add Product to Cart</button>
    </div>
  );
}

function FooterWithHooks() {
  const { user } = useUser();
  return <footer>© 2024 {user.name}'s App</footer>;
}

// 🟢 SOLUTION 3: Component Composition
function CompositionSolution() {
  const [user, setUser] = useState({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    preferences: { theme: 'dark', language: 'en', notifications: true }
  });
  
  const [cartItems, setCartItems] = useState([
    { id: 1, name: 'Laptop', quantity: 1, price: 999 }
  ]);
  
  return (
    <div>
      <h2>🟢 Solution 3: Component Composition</h2>
      <LayoutComposition
        header={
          <>
            <UserInfo user={user} />
            <CartIcon cartItems={cartItems} />
          </>
        }
        main={
          <>
            <UserDashboard user={user} onUpdateUser={setUser} />
            <ProductSection cartItems={cartItems} onUpdateCart={setCartItems} />
          </>
        }
        footer={<Footer user={user} />}
      />
    </div>
  );
}

function LayoutComposition({ header, main, footer }) {
  return (
    <div className="layout">
      <header>
        <h1>My App</h1>
        {header}
      </header>
      <main>{main}</main>
      {footer}
    </div>
  );
}

// Main component showing all examples
export default function PropDrillingExample() {
  return (
    <div>
      <PropDrillingExample />
      <hr />
      <ContextSolution />
      <hr />
      <CustomHooksSolution />
      <hr />
      <CompositionSolution />
    </div>
  );
}
```
</details>
<details>
<summary><strong>26. How do you handle asynchronous operations in React?</strong></summary>

**Answer:**
Asynchronous operations in React are handled using useEffect for side effects, async/await for API calls, and state management for loading states. You should handle loading, success, and error states appropriately, and clean up any ongoing operations when components unmount.[^2]

**Example:**

```jsx
import React, { useState, useEffect, useCallback, useRef } from 'react';

// 1. Basic API call with useEffect
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let isMounted = true; // Flag to prevent state updates if component unmounts
    
    async function fetchUser() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(`/api/users/${userId}`);
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const userData = await response.json();
        
        // Only update state if component is still mounted
        if (isMounted) {
          setUser(userData);
        }
      } catch (err) {
        if (isMounted) {
          setError(err.message);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    }
    
    fetchUser();
    
    // Cleanup function
    return () => {
      isMounted = false;
    };
  }, [userId]);
  
  if (loading) return <div>Loading user...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;
  
  return (
    <div className="user-profile">
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Joined: {new Date(user.createdAt).toLocaleDateString()}</p>
    </div>
  );
}

// 2. Custom hook for data fetching
function useApi(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      
      const response = await fetch(url);
      
      if (!response.ok) {
        throw new Error(`Failed to fetch: ${response.statusText}`);
      }
      
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, [url]);
  
  useEffect(() => {
    fetchData();
  }, [fetchData]);
  
  const refetch = useCallback(() => {
    fetchData();
  }, [fetchData]);
  
  return { data, loading, error, refetch };
}

// Using the custom hook
function PostsList() {
  const { data: posts, loading, error, refetch } = useApi('/api/posts');
  
  if (loading) return <div>Loading posts...</div>;
  if (error) return <div>Error: {error} <button onClick={refetch}>Retry</button></div>;
  
  return (
    <div>
      <h2>Posts</h2>
      <button onClick={refetch}>Refresh</button>
      {posts?.map(post => (
        <div key={post.id} className="post">
          <h3>{post.title}</h3>
          <p>{post.content}</p>
        </div>
      ))}
    </div>
  );
}

// 3. Handling form submission with async operations
function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [submitStatus, setSubmitStatus] = useState(null);
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    setIsSubmitting(true);
    setSubmitStatus(null);
    
    try {
      const response = await fetch('/api/contact', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(formData),
      });
      
      if (!response.ok) {
        throw new Error('Failed to submit form');
      }
      
      await response.json();
      setSubmitStatus({ type: 'success', message: 'Message sent successfully!' });
      setFormData({ name: '', email: '', message: '' }); // Reset form
    } catch (error) {
      setSubmitStatus({ 
        type: 'error', 
        message: error.message || 'Failed to send message' 
      });
    } finally {
      setIsSubmitting(false);
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <h2>Contact Us</h2>
      
      <div>
        <label>Name:</label>
        <input
          type="text"
          name="name"
          value={formData.name}
          onChange={handleChange}
          required
        />
      </div>
      
      <div>
        <label>Email:</label>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          required
        />
      </div>
      
      <div>
        <label>Message:</label>
        <textarea
          name="message"
          value={formData.message}
          onChange={handleChange}
          required
          rows={4}
        />
      </div>
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Sending...' : 'Send Message'}
      </button>
      
      {submitStatus && (
        <div className={`status ${submitStatus.type}`}>
          {submitStatus.message}
        </div>
      )}
    </form>
  );
}

// 4. Advanced pattern: Using AbortController for cancellation
function SearchableUserList() {
  const [searchTerm, setSearchTerm] = useState('');
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const abortControllerRef = useRef(null);
  
  const searchUsers = useCallback(async (query) => {
    // Cancel previous request if it exists
    if (abortControllerRef.current) {
      abortControllerRef.current.abort();
    }
    
    // Create new AbortController for this request
    abortControllerRef.current = new AbortController();
    
    try {
      setLoading(true);
      setError(null);
      
      const response = await fetch(`/api/users/search?q=${encodeURIComponent(query)}`, {
        signal: abortControllerRef.current.signal
      });
      
      if (!response.ok) {
        throw new Error('Search failed');
      }
      
      const searchResults = await response.json();
      setUsers(searchResults);
    } catch (err) {
      if (err.name !== 'AbortError') {
        setError(err.message);
      }
    } finally {
      setLoading(false);
    }
  }, []);
  
  // Debounced search
  useEffect(() => {
    if (searchTerm.trim() === '') {
      setUsers([]);
      return;
    }
    
    const timeoutId = setTimeout(() => {
      searchUsers(searchTerm);
    }, 500); // 500ms debounce
    
    return () => clearTimeout(timeoutId);
  }, [searchTerm, searchUsers]);
  
  // Cleanup on unmount
  useEffect(() => {
    return () => {
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }
    };
  }, []);
  
  return (
    <div>
      <h2>Search Users</h2>
      
      <input
        type="text"
        placeholder="Search users..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
      />
      
      {loading && <p>Searching...</p>}
      {error && <p>Error: {error}</p>}
      
      <div className="user-results">
        {users.map(user => (
          <div key={user.id} className="user-card">
            <h3>{user.name}</h3>
            <p>{user.email}</p>
          </div>
        ))}
      </div>
      
      {searchTerm && !loading && users.length === 0 && (
        <p>No users found for "{searchTerm}"</p>
      )}
    </div>
  );
}

// 5. Handling multiple async operations
function Dashboard() {
  const [data, setData] = useState({
    user: null,
    stats: null,
    notifications: null
  });
  const [loading, setLoading] = useState({
    user: true,
    stats: true,
    notifications: true
  });
  const [errors, setErrors] = useState({});
  
  useEffect(() => {
    async function loadDashboardData() {
      const updateLoading = (key, value) => {
        setLoading(prev => ({ ...prev, [key]: value }));
      };
      
      const updateError = (key, error) => {
        setErrors(prev => ({ ...prev, [key]: error }));
      };
      
      const updateData = (key, value) => {
        setData(prev => ({ ...prev, [key]: value }));
      };
      
      // Load all data in parallel
      const promises = [
        fetch('/api/user').then(r => r.json()).then(data => {
          updateData('user', data);
          updateLoading('user', false);
        }).catch(err => {
          updateError('user', err.message);
          updateLoading('user', false);
        }),
        
        fetch('/api/stats').then(r => r.json()).then(data => {
          updateData('stats', data);
          updateLoading('stats', false);
        }).catch(err => {
          updateError('stats', err.message);
          updateLoading('stats', false);
        }),
        
        fetch('/api/notifications').then(r => r.json()).then(data => {
          updateData('notifications', data);
          updateLoading('notifications', false);
        }).catch(

<div style="text-align: center">⁂</div>

[^1]: https://www.interviewbit.com/react-interview-questions/
[^2]: https://www.geeksforgeeks.org/reactjs/react-interview-questions/
[^3]: https://www.turing.com/interview-questions/react-js
[^4]: https://www.simplilearn.com/tutorials/reactjs-tutorial/reactjs-interview-questions
[^5]: https://www.greatfrontend.com/blog/100-react-interview-questions-straight-from-ex-interviewers
[^6]: https://github.com/sudheerj/reactjs-interview-questions
[^7]: https://dev.to/ruppysuppy/17-react-interview-questions-you-must-know-as-a-developer-in-2025-1o6f
[^8]: https://www.greatfrontend.com/questions/react-interview-questions
[^9]: https://www.youtube.com/watch?v=CAsTwrYx8pM```

