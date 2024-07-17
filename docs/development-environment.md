# AITaskMaster Development Environment Setup
Welcome to the development environment setup for AITaskMaster! This guide will walk you through setting up all the necessary tools and frameworks to start building your AI-powered task management application.

???+ abstract "Step 1: Install Python 3.9+"

	AITaskMaster requires Python 3.9 or newer. Let's check your current Python version:
	
	**$ python --version**
 	
	Python 3.9.5
 	
	If you see a version lower than 3.9, please [download and install the latest Python version.](https://www.python.org/downloads/)

???+ abstract "Step 2: Set up a virtual environment"

	Create a new directory for your project and set up a virtual environment:
	
	**$ mkdir aitaskmaster**
 	
	**$ cd aitaskmaster**
 	
	**$ python -m venv venv**
 	
	**$ source venv/bin/activate # On Windows, use `venv\Scripts\activate`**
 	
	You should now see (venv) at the beginning of your command prompt, indicating that the virtual environment is active.

???+ abstract "Step 3: Install FastAPI and dependencies"

	Now, let's install FastAPI and other necessary packages:
	
	**$ pip install fastapi[all] sqlalchemy psycopg2-binary uvicorn**
 	
	This command installs FastAPI with all optional dependencies, SQLAlchemy for database operations, psycopg2 for PostgreSQL support, and uvicorn as the ASGI server.

???+ abstract "Step 4: Install PostgreSQL"

	Download and install PostgreSQL from the [official website.](https://www.postgresql.org/download/) After installation, create a new database for AITaskMaster:
	
	**$ psql -U postgres**
 	
	**postgres=# CREATE DATABASE aitaskmaster;**
	
 	**postgres=# \q**
	
 	Make sure to **remember the password you set for the PostgreSQL user**, as you'll need it later in the project setup.

???+ abstract "Step 5: Set up Git for version control"

	If you haven't already, [install Git.](https://git-scm.com/downloads) Then, initialize a new Git repository in your project folder:
	
	**$ git init**
	
 	**$ git add .**
	
 	**$ git commit -m "Initial commit"**
	
 	Create a .gitignore file to exclude unnecessary files from version control:
	
	**# .gitignore**
	
 	**venv/**
	
 	"__pycache__/"
	
 	__*.pyc__
	
 	**.env**
  	
  
## **AI Assistant**
Great job setting up your development environment! Here's a quick Python script to verify that everything is working correctly. Create a file named test_setup.py and paste the following code:

``` { .yaml .copy }
from fastapi import FastAPI
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

app = FastAPI()
Base = declarative_base()

class TestModel(Base):
    __tablename__ = "test_table"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)

DATABASE_URL = "postgresql://postgres:your_password@localhost/aitaskmaster"
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base.metadata.create_all(bind=engine)

@app.get("/")
def read_root():
    return {"Hello": "AITaskMaster"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

Replace **'your_password'** with your actual **PostgreSQL password**. Then run the script:

**$ python test_setup.py**

If everything is set up correctly, you should see output indicating that the server is running. You can then visit [http://localhost:8000](http://localhost:8000) in your browser to see the JSON response.

Congratulations! You've successfully set up your development environment for AITaskMaster. You're now ready to start building your AI-powered task management application!
