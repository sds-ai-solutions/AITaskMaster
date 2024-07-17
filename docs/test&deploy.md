# **Testing and Deployment for AITaskMaster**
Welcome to the final phase of AITaskMaster development! In this crucial stage, we'll ensure the quality and reliability of our application through comprehensive testing, and then deploy it to a production environment.

## **1. Testing**
### **Backend Testing**
Let's start by writing unit tests for our FastAPI backend. Create a new file test_main.py in the tests directory:

``` { .yaml .copy }
# tests/test_main.py
from fastapi.testclient import TestClient
from app.main import app
from app.database import get_db, Base
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
import pytest

SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def override_get_db():
    try:
        db = TestingSessionLocal()
        yield db
    finally:
        db.close()

app.dependency_overrides[get_db] = override_get_db

client = TestClient(app)

@pytest.fixture(scope="function")
def test_db():
    Base.metadata.create_all(bind=engine)
    yield
    Base.metadata.drop_all(bind=engine)

def test_create_user(test_db):
    response = client.post(
        "/users/",
        json={"email": "test@example.com", "password": "testpassword"}
    )
    assert response.status_code == 200
    data = response.json()
    assert data["email"] == "test@example.com"
    assert "id" in data

def test_create_task(test_db):
    # First, create a user
    user_response = client.post(
        "/users/",
        json={"email": "test@example.com", "password": "testpassword"}
    )
    user_id = user_response.json()["id"]

    # Then, create a task for this user
    response = client.post(
        "/tasks/",
        json={
            "title": "Test Task",
            "description": "This is a test task",
            "due_date": "2023-12-31T23:59:59",
            "user_id": user_id
        }
    )
    assert response.status_code == 200
    data = response.json()
    assert data["title"] == "Test Task"
    assert "priority" in data
    assert "estimated_time" in data

# Add more tests for other endpoints and edge cases
```

> These tests ensure that our user creation and task creation endpoints are working correctly, including the AI-powered priority and time estimation features.

### **Frontend Testing**
For the React frontend, we'll use Jest and React Testing Library. Create a test file for the Dashboard component:

``` { .yaml .copy }
// src/components/Dashboard.test.js
import React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Dashboard from './Dashboard';
import * as api from '../services/api';

jest.mock('../services/api');

describe('Dashboard', () => {
  beforeEach(() => {
    api.getTasks.mockResolvedValue([
      { id: 1, title: 'Test Task', priority: 3, estimated_time: 2, due_date: '2023-12-31T23:59:59' }
    ]);
    api.createTask.mockResolvedValue({ id: 2, title: 'New Task', priority: 2, estimated_time: 1, due_date: '2023-12-31T23:59:59' });
  });

  test('renders task list and form', async () => {
    render();
    
    await waitFor(() => {
      expect(screen.getByText('Test Task')).toBeInTheDocument();
    });
    
    expect(screen.getByText('Add Task')).toBeInTheDocument();
  });

  test('creates new task', async () => {
    render();
    
    userEvent.type(screen.getByLabelText('Task Title'), 'New Task');
    userEvent.type(screen.getByLabelText('Description'), 'This is a new task');
    userEvent.type(screen.getByLabelText('Due Date'), '2023-12-31');
    userEvent.click(screen.getByText('Add Task'));

    await waitFor(() => {
      expect(screen.getByText('New Task')).toBeInTheDocument();
    });
  });
});
```

> These tests ensure that our Dashboard component renders correctly and can create new tasks.

## **2. Deployment**
### **Backend Deployment**
We'll deploy our FastAPI backend to a cloud platform like Heroku. First, create a Procfile in your backend directory:

``` { .yaml .copy }
web: uvicorn app.main:app --host=0.0.0.0 --port=${PORT:-5000}
```

Then, deploy to Heroku:

``` { .yaml .copy }
heroku create aitaskmaster-backend
git push heroku main
```

### **Frontend Deployment**
For the React frontend, we'll use Netlify. First, build your React app:

``` { .yaml .copy }
npm run build
```

Then, deploy to Netlify:

``` { .yaml .copy }
netlify deploy --prod
```

### **Database Deployment**
For the production database, we'll use a managed PostgreSQL service like Heroku Postgres. Add it to your Heroku app:

``` { .yaml .copy }
heroku addons:create heroku-postgresql:hobby-dev
```

Update your backend code to use the DATABASE_URL environment variable provided by Heroku.

### **Continuous Integration/Continuous Deployment (CI/CD)**
To automate our testing and deployment process, we'll set up a CI/CD pipeline using GitHub Actions. Create a file .github/workflows/main.yml:

``` { .yaml .copy }
name: CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Run tests
      run: pytest

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
    - uses: actions/checkout@v2
    - name: Deploy to Heroku
      uses: akhileshns/heroku-deploy@v3.12.12
      with:
        heroku_api_key: ${{secrets.HEROKU_API_KEY}}
        heroku_app_name: "aitaskmaster-backend"
        heroku_email: "your-email@example.com"
```

> This workflow runs our tests on every push and pull request, and deploys to Heroku when changes are pushed to the main branch.

## **Deployment Architecture**

![AITaskMaster Deployment Architecture showing frontend on Netlify, backend on Heroku, and database on Heroku Postgres](https://github.com/sds-ai-solutions/AITaskMaster/blob/8d327bb37121588c81bae744109f455f18fdceee/docs/images/Deployment-Architecture.png)

## **AI Assistant**
> Excellent work on setting up testing and deployment for AITaskMaster! Here are some additional considerations:
>
> - Implement end-to-end testing using tools like Cypress to test the entire application flow.
> - Set up monitoring and logging using services like Sentry or ELK stack to track errors and performance in production.
> - Implement a staging environment to test changes before deploying to production.
> - Consider using Docker to containerize your application for consistent deployments.
> - Implement database migrations to manage schema changes safely.
> - Set up regular backups for your production database.
> 
> Remember to keep your deployment keys and sensitive information secure, and never commit them to your repository.

Congratulations! You've successfully set up testing and deployment for AITaskMaster. Your AI-powered task management system is now ready for users to enjoy. Remember to monitor its performance, gather user feedback, and continue improving the system over time.
