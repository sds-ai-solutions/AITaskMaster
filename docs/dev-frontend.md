# **Frontend Development for AITaskMaster**
Welcome to the frontend development phase of AITaskMaster! In this stage, we'll create an intuitive and responsive user interface using React and Material-UI. Our frontend will communicate with the FastAPI backend and showcase the AI-powered features we've implemented.

## **Project Setup**
First, let's set up our React project using Create React App:

``` { .yaml .copy }
npx create-react-app aitaskmaster-frontend
cd aitaskmaster-frontend
npm install @material-ui/core @material-ui/icons axios react-router-dom
```

## **Project Structure**
Our frontend project structure will look like this:

``` { .yaml .copy }
aitaskmaster-frontend/
│
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   ├── Dashboard.js
│   │   ├── TaskList.js
│   │   ├── TaskForm.js
│   │   ├── ProjectList.js
│   │   └── ProjectForm.js
│   ├── services/
│   │   └── api.js
│   ├── App.js
│   └── index.js
└── package.json
```

## **Main App Component**
Let's start by creating the main App component:

``` { .yaml .copy }
// src/App.js
import React from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import { ThemeProvider, createTheme } from '@material-ui/core/styles';
import CssBaseline from '@material-ui/core/CssBaseline';
import Dashboard from './components/Dashboard';

const theme = createTheme({
  palette: {
    primary: {
      main: '#2c3e50',
    },
    secondary: {
      main: '#2ecc71',
    },
  },
});

function App() {
  return (
    
      
      
        
          
        
      
    
  );
}

export default App;
```

> This sets up the main App component with routing and a custom Material-UI theme.

## **Dashboard Component**
Now, let's create the Dashboard component, which will be the main interface of our application:

``` { .yaml .copy }
// src/components/Dashboard.js
import React, { useState, useEffect } from 'react';
import { Container, Grid, Paper, Typography } from '@material-ui/core';
import TaskList from './TaskList';
import TaskForm from './TaskForm';
import { getTasks, createTask } from '../services/api';

function Dashboard() {
  const [tasks, setTasks] = useState([]);

  useEffect(() => {
    fetchTasks();
  }, []);

  const fetchTasks = async () => {
    const fetchedTasks = await getTasks();
    setTasks(fetchedTasks);
  };

  const handleCreateTask = async (taskData) => {
    const newTask = await createTask(taskData);
    setTasks([...tasks, newTask]);
  };

  return (
    
      
        AITaskMaster Dashboard
      
      
        
          
            
          
        
        
          
            
          
        
      
    
  );
}

export default Dashboard;
```

> The Dashboard component fetches tasks from the API and provides a form to create new tasks. It uses the TaskList and TaskForm components to display and manage tasks.

## **TaskList Component**
Let's create the TaskList component to display our AI-prioritized tasks:

``` { .yaml .copy }
// src/components/TaskList.js
import React from 'react';
import { List, ListItem, ListItemText, ListItemSecondaryAction, Chip } from '@material-ui/core';
import { formatDistanceToNow } from 'date-fns';

function TaskList({ tasks }) {
  return (
    
      {tasks.map((task) => (
        
          
          
            
            
          
        
      ))}
    
  );
}

export default TaskList;
```

> This component displays tasks in a list, showing their title, due date, AI-predicted priority, and estimated time.

## **TaskForm Component**
Now, let's create the TaskForm component for adding new tasks:

``` { .yaml .copy }
// src/components/TaskForm.js
import React, { useState } from 'react';
import { TextField, Button, Grid } from '@material-ui/core';

function TaskForm({ onSubmit }) {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');
  const [dueDate, setDueDate] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit({ title, description, due_date: dueDate });
    setTitle('');
    setDescription('');
    setDueDate('');
  };

  return (
    

      
        
           setTitle(e.target.value)}
            required
          />
        
        
           setDescription(e.target.value)}
            multiline
            rows={4}
          />
        
        
           setDueDate(e.target.value)}
            InputLabelProps={{
              shrink: true,
            }}
            required
          />
        
        
          
           Add Task
          
        
      
    

  );
}

export default TaskForm;
```

> This form allows users to input task details. When submitted, it calls the onSubmit function provided by the parent component, which will create a new task using our AI-powered backend.

## **API Service**
Finally, let's create an API service to communicate with our FastAPI backend:

``` { .yaml .copy }
// src/services/api.js
import axios from 'axios';

const API_URL = 'http://localhost:8000';  // Replace with your actual API URL

export const getTasks = async () => {
  const response = await axios.get(`${API_URL}/tasks/`);
  return response.data;
};

export const createTask = async (taskData) => {
  const response = await axios.post(`${API_URL}/tasks/`, taskData);
  return response.data;
};

// Add more API functions as needed
```

> This service uses Axios to make HTTP requests to our FastAPI backend. It provides functions to get all tasks and create a new task.

## **UI Preview**

![AITaskMaster UI Preview showing the dashboard with task list and task form](https://github.com/sds-ai-solutions/AITaskMaster/blob/main/docs/images/AITaskMaster-UI-Preview.png)

AITaskMaster UI Preview showing the dashboard with task list and task form

## **AI Assistant**
> Great progress on the frontend development for AITaskMaster! Here are some suggestions for further improvements:
>
> - Implement user authentication and display user-specific tasks.
> - Add a projects view to group related tasks together.
> - Create a settings page where users can customize AI behavior.
> - Implement real-time updates using WebSockets for instant task updates.
> - Add data visualizations to show task completion trends and AI accuracy over time.
> - Implement drag-and-drop functionality for easy task reordering.
>
> Remember to thoroughly test your frontend, especially the integration with the AI-powered backend features.

Congratulations! You've successfully developed the frontend for AITaskMaster. This user-friendly interface will allow users to interact with your AI-powered task management system effectively.
