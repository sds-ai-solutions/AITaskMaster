# AITaskMaster Database Schema Designer
Welcome to the Database Schema Designer for AITaskMaster! Here, we'll create an efficient and scalable database structure to support your AI-powered task management application. Our AI has analyzed your requirements and suggests the following schema:

| **Users**                         | **Tasks**                              | **Projects**                        |
| --------------------------------- | -------------------------------------- | ----------------------------------- |
| **id:** Integer (Primary Key)     | **id:** Integer (Primary Key)          | **id:** Integer (Primary Key)       |
| **username:** String (Unique)     | **title:** String                      | **name:** String                    |
| **email:** String (Unique)        | **description:** Text                  | **description:** Text               |
| **password_hash:** String         | **status:** Enum                       | **start_date:** DateTime            |
| **created_at:** DateTime          | **priority:** Integer                  | **end_date:** DateTime              |
| _Has many: Tasks, Projects_       | **estimated_time:** Float              | **user_id:** Integer (Foreign Key)  |
|                                   | **due_date:** DateTime                 | _Belongs to: User_                  |
|                                   | **user_id:** Integer (Foreign Key)     | _Has many: Tasks_                   |
|                                   | **project_id:** Integer (Foreign Key)  |                                     |
|                                   | _Belongs to: User, Project_            |                                     |

## **SQLAlchemy Models**
Here's the Python code to implement these models using SQLAlchemy:

``` { .yaml .copy }
from sqlalchemy import Column, Integer, String, Text, DateTime, Float, ForeignKey, Enum
from sqlalchemy.orm import relationship
from sqlalchemy.ext.declarative import declarative_base
from datetime import datetime
import enum

Base = declarative_base()

class TaskStatus(enum.Enum):
    TODO = "To Do"
    IN_PROGRESS = "In Progress"
    DONE = "Done"

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True)
    email = Column(String, unique=True, index=True)
    password_hash = Column(String)
    created_at = Column(DateTime, default=datetime.utcnow)

    tasks = relationship("Task", back_populates="user")
    projects = relationship("Project", back_populates="user")

class Task(Base):
    __tablename__ = "tasks"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(Text)
    status = Column(Enum(TaskStatus))
    priority = Column(Integer)
    estimated_time = Column(Float)
    due_date = Column(DateTime)
    user_id = Column(Integer, ForeignKey("users.id"))
    project_id = Column(Integer, ForeignKey("projects.id"))

    user = relationship("User", back_populates="tasks")
    project = relationship("Project", back_populates="tasks")

class Project(Base):
    __tablename__ = "projects"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    description = Column(Text)
    start_date = Column(DateTime)
    end_date = Column(DateTime)
    user_id = Column(Integer, ForeignKey("users.id"))

    user = relationship("User", back_populates="projects")
    tasks = relationship("Task", back_populates="project")
```



## **AI Assistant**
This schema provides a solid foundation for your AITaskMaster application. Here are some key points to consider:

- The User model allows for user authentication and links users to their tasks and projects.
- The Task model includes fields for AI-driven features like priority and estimated time.
- The Project model helps organize tasks and can be used for timeline visualizations.
- Relationships between models are established using SQLAlchemy's relationship function, allowing for efficient querying.

To implement this schema, create a new file named models.py in your project directory and paste the provided code. Then, update your database connection code to create these tables:

``` { .yaml .copy }
from sqlalchemy import create_engine
from models import Base

DATABASE_URL = "postgresql://username:password@localhost/aitaskmaster"
engine = create_engine(DATABASE_URL)
Base.metadata.create_all(bind=engine)
```
  
Remember to replace '**username**' and '**password**' with your actual **PostgreSQL credentials**.

Now that we have our database schema designed and implemented, we're ready to start building the core backend functionality of AITaskMaster!
