<<<<<<< HEAD
# QueryGenie – Finance Loan Analytics AI Agent

## Overview

QueryGenie is a Generative AI powered Text-to-SQL application that enables users to interact with a Finance Loan Database using natural language queries.

Instead of writing SQL queries manually, users can ask questions such as:

- What is the average loan amount?
- Show high-risk customers.
- What is the average interest rate?
- Show verified customers.
- Plot loan amount by purpose.

The application automatically:

1. Converts natural language into SQL queries.
2. Executes SQL queries on MySQL.
3. Retrieves results from the Finance Loan Database.
4. Returns results in human-readable format.
5. Generates interactive Plotly visualizations when requested.

---

# Project Architecture

```text
User
  |
  v
Streamlit Chat UI
  |
  v
SQL Agent (LangChain + Groq LLM)
  |
  v
MySQL Finance Loan Database
  |
  v
Natural Language Response
  |
  v
Python Agent
  |
  v
Plotly Visualization
```

---

# Tech Stack

## Programming Language

- Python 3.11

## Database

- MySQL
- SQLAlchemy

## Generative AI

- LangChain
- Groq API
- GPT-OSS-120B

## Frontend

- Streamlit

## Visualization

- Plotly

## Data Processing

- Pandas

## Memory Management

- SQLChatMessageHistory
- ConversationBufferMemory

## Utilities

- Python REPL Tool
- Regular Expressions (Regex)
- Environment Variables (.env)

---

# Finance Loan Database

Database Name:

```sql
bank_loan_database
```

Dataset Columns:

```text
member_id
purpose
sub_grade
term
verification_status
annual_income
dti
installment
int_rate
loan_amount
total_acc
total_payment
issue_date
```

---

# Project Structure

```text
QueryGenie
│
├── src
│   ├── agent.py
│   ├── chat_app.py
│   └── helper.py
│
├── .env
├── requirements.txt
├── README.md
│
└── Finance Loan Database
```

---

# Installation Process

## Step 1: Clone Repository

```bash
git clone <repository_url>
cd QueryGenie
```

## Step 2: Create Virtual Environment

```bash
python -m venv venv
```

Activate Environment

Windows:

```bash
venv\Scripts\activate
```

Linux/Mac:

```bash
source venv/bin/activate
```

---

## Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Step 4: Install MySQL

Download MySQL:

https://dev.mysql.com/downloads/mysql/

Download MySQL Workbench:

https://dev.mysql.com/downloads/workbench/

---

## Step 5: Create Database

```sql
CREATE DATABASE bank_loan_database;
```

Use Database

```sql
USE bank_loan_database;
```

---

## Step 6: Import Loan Dataset

Import your finance loan CSV dataset into MySQL Workbench using:

```text
Table Data Import Wizard
```

---

## Step 7: Configure Environment Variables

Create a `.env` file:

```env
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_password

DB_NAME=bank_loan_database

GROQ_API_KEY=your_api_key

MLFLOW_TRACKING_URI=./mlruns
```

---

## Step 8: Run Application

```bash
streamlit run src/chat_app.py
```

Application URL:

```text
http://localhost:8501
```

---

# Project Workflow

## Step 1

User enters a question.

Example:

```text
What is the average loan amount?
```

---

## Step 2

The SQL Agent receives the question.

---

## Step 3

The LLM converts the question into SQL.

Example:

```sql
SELECT AVG(loan_amount)
FROM bank_loan_data;
```

---

## Step 4

The query is executed on MySQL.

---

## Step 5

Results are retrieved.

Example:

```text
Average Loan Amount = ₹15,200
```

---

## Step 6

The response is displayed in Streamlit.

---

# Plot Generation Workflow

User asks:

```text
Plot average loan amount by purpose
```

---

SQL Agent:

```sql
SELECT purpose,
AVG(loan_amount)
FROM bank_loan_data
GROUP BY purpose;
```

---

Python Agent:

Generates Plotly visualization code.

---

Streamlit:

Displays interactive charts.

---

# File Explanation

## 1. agent.py

Main brain of the application.

Responsibilities:

- LLM Initialization
- Database Connection
- SQL Agent Creation
- Python Agent Creation
- Memory Management

### get_chat_openai()

Creates the Groq LLM connection.

```python
ChatOpenAI(
    model_name=model_name,
    api_key=GROQ_API_KEY,
    base_url=GROQ_BASE_URL
)
```

Purpose:

- Connects LangChain with Groq.

---

### get_sql_toolkit()

Creates SQLDatabaseToolkit.

Responsibilities:

- View Tables
- View Schema
- Execute SQL Queries

---

### get_agent_llm()

Returns the LLM instance used by the SQL Agent.

---

### create_agent_for_sql()

Creates a LangChain SQL Agent.

Uses:

```python
create_sql_agent()
```

Responsibilities:

- SQL Query Generation
- SQL Execution
- Response Generation

---

### SQLChatMessageHistory

Stores conversations inside MySQL.

Table:

```sql
message_store
```

Benefits:

- Persistent Memory
- Session Tracking

---

### ConversationBufferMemory

Stores previous conversations.

Benefits:

- Context Awareness
- Follow-up Question Handling

Example:

User:

```text
Show average loan amount
```

Then:

```text
Only for verified customers
```

Agent understands the context.

---

### CUSTOM_SUFFIX

Prompt Engineering Layer.

Rules:

- No Hallucination
- Database-only answers
- Use LOWER() for text matching
- Return actual query results
- Return "No Results Found" if data does not exist

---

### create_agent_for_python()

Creates Visualization Agent.

Uses:

```python
PythonREPLTool()
```

Responsibilities:

- Generate Plotly Code
- Create Interactive Charts

---

## 2. chat_app.py

Frontend layer built using Streamlit.

Responsibilities:

- Chat Interface
- User Input Handling
- Agent Routing
- Response Rendering

---

### Session State

Stores:

```python
st.session_state
```

Used for:

- Chat History
- Agent Persistence

---

### generate_response()

Main routing function.

Decision Flow:

```text
User Query
      |
      |
      +---- SQL Question ----> SQL Agent
      |
      +---- Chart Request ---> SQL Agent ---> Python Agent
```

---

### Plot Detection

Keywords:

```text
plot
chart
graph
diagram
```

---

### Error Handling

Uses:

```python
try:
except:
```

Returns:

```text
NO_RESPONSE
```

instead of crashing.

---

### Reset Chat

Clears:

```python
st.session_state.messages
```

Creates fresh agents.

---

## 3. helper.py

Utility functions.

---

### display_text_with_images(text)

Displays text and image URLs.

Responsibilities:

- Detect image URLs using Regex
- Split text and image content
- Display text using Streamlit Markdown
- Display images using Streamlit Image Component

Uses:

```python
re.findall()
re.split()
```

---

### display_python_code_plots(text)

Extracts Plotly code from LLM responses.

Regex Pattern:

```python
pattern = r'```python\s(.*?)```'
```

Returns:

- Plotly Code
- None (if code block not found)

Purpose:

- Extract executable Python code generated by the Python Agent.

---

# Database Analysis Examples

## Average Loan Amount

```sql
SELECT AVG(loan_amount)
FROM bank_loan_data;
```

---

## Average Interest Rate

```sql
SELECT AVG(int_rate)
FROM bank_loan_data;
```

---

## Loan Distribution by Purpose

```sql
SELECT purpose,
COUNT(*)
FROM bank_loan_data
GROUP BY purpose;
```

---

## Verified Customers

```sql
SELECT *
FROM bank_loan_data
WHERE verification_status='Verified';
```

---

## Total Payments

```sql
SELECT SUM(total_payment)
FROM bank_loan_data;
```

---

# Concepts Used

## SQL Concepts

- SELECT
- WHERE
- GROUP BY
- HAVING
- ORDER BY
- Aggregate Functions
- Subqueries
- Joins

## Python Concepts

- Functions
- OOP
- Exception Handling
- Regex
- Environment Variables

---

## Data Analytics Concepts

- Loan Analysis
- Risk Analysis
- Customer Segmentation
- Financial Analytics
- Interest Rate Analysis

## Generative AI Concepts

### Large Language Models (LLMs)

Used to understand user questions and generate SQL.

---

### Prompt Engineering

Custom instructions added to improve accuracy and prevent hallucinations.

---

### Text-to-SQL

Converts natural language into SQL queries.

Example:

```text
What is average loan amount?
```

↓

```sql
SELECT AVG(loan_amount)
FROM bank_loan_data;
```

---

### Agentic AI

Uses specialized agents.

Agent 1:

```text
SQL Agent
```

Agent 2:

```text
Python Agent

### Multi-Agent Architecture

Benefits:

- Better Accuracy
- Easier Maintenance
- Separation of Concerns
- Improved Reliability

# Challenges Faced

- MySQL Authentication Errors
- Environment Variable Configuration
- Groq API Configuration
- Model Compatibility Issues
- SQL Agent Prompt Tuning
- Memory Persistence Setup

---

# Future Improvements

- Authentication System
- Role-Based Access
- PDF Report Generation
- Dashboard Export
- PostgreSQL Support
- RAG Integration
- Vector Database Integration
- Secure Sandbox Execution
- Multi-Database Support

---

# Interview Questions

### Why LangChain?
Provides Agents, Tools, Memory, Prompt Templates, and Database Integrations.

### Why SQL Agent?
Converts natural language into executable SQL.

### Why Python Agent?
Generates visualizations from SQL output.

### Why Streamlit?
Provides fast and simple UI development.

### Why Groq?
Provides fast inference with OpenAI-compatible APIs.

### Why Memory?
Maintains context across user interactions.
### Why Multi-Agent Architecture?
Separates SQL reasoning and visualization logic, improving reliability and maintainability
# Author

## Anushri Jitendra Bagal

Aspiring Data Analyst | Data Engineer | AI/ML Enthusiast

### Skills

- Python
- SQL
- MySQL
- Power BI
- Machine Learning
- LangChain
- Generative AI
- Streamlit
- FastAPI
- Docker
- MLOps
- Data Analytics
=======
# QueryGenie-Text-to-SQL
>>>>>>> af42ec864d0bdcf8e74085a78a1b7254b5b9ba44
