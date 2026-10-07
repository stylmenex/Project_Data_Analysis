# Employee Data Analysis

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![pandas](https://img.shields.io/badge/pandas-data%20analysis-150458)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-database-336791)
![Jupyter](https://img.shields.io/badge/Jupyter-notebook-F37626)

A Python project that loads employee data from a CSV file, cleans and analyzes it with **pandas**, models each employee as an object using a custom **`Employee`** class, and stores the final data in a **PostgreSQL** database using **psycopg2**.

## Table of Contents

- [Features](#features)
- [Project Structure](#project-structure)
- [Dataset](#dataset)
- [Technologies Used](#technologies-used)
- [Getting Started](#getting-started)
- [Database Configuration](#database-configuration)
- [How It Works](#how-it-works)
- [Using the Database Backup](#using-the-database-backup)
- [What I Learned](#what-i-learned)

## Features

- Reads and previews a CSV dataset with pandas
- Cleans missing values (`NaN`) by replacing them with the column mean
- Converts the `age` column from decimal numbers to integers
- Calculates descriptive statistics (count, mean, min, max, quartiles) for `age` and `salary`
- Filters employees older than 30
- Represents each row as an `Employee` object (object-oriented programming)
- Supports giving a bonus and changing an employee's department
- Creates a table in PostgreSQL and inserts all records into it

## Project Structure

```
.
├── project_data_analysis.ipynb   # Main notebook with all the code
├── pythondata.csv                # Input dataset (200 employees)
├── database.sql                  # Backup of the PostgreSQL database
├── requirements.txt              # Python dependencies
└── README.md                     # Project documentation
```

## Dataset

The file `pythondata.csv` contains **200 employee records** with the following columns:

| Column       | Description                     | Example                 |
|--------------|---------------------------------|-------------------------|
| `first_name` | Employee's first name           | Νίκος                   |
| `last_name`  | Employee's last name            | Σαμαράς                 |
| `age`        | Employee's age                  | 39                      |
| `salary`     | Employee's salary in euros      | 73003.0                 |
| `department` | Department the employee works in | Software Development  |

Some values in `age` and `salary` are missing and are handled in the data cleaning step.

## Technologies Used

- **Python 3.9+**
- **pandas** – reading, cleaning and analyzing data
- **psycopg2** – connecting Python to PostgreSQL
- **PostgreSQL** – storing the final data (hosted on [Neon](https://neon.tech) during development)
- **Jupyter Notebook** – interactive development environment

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/your-username/your-repository.git
cd your-repository
```

### 2. (Recommended) Create a virtual environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate
```

### 3. Install the dependencies

```bash
pip install -r requirements.txt
```

### 4. Open the notebook

```bash
jupyter notebook project_data_analysis.ipynb
```

You can also open the notebook directly in VS Code.

> **Important:** `pythondata.csv` must be in the same folder as the notebook, because the notebook reads it using a relative path.

## Database Configuration

The notebook connects to PostgreSQL with `psycopg2`. Before running the database cells, replace the placeholder values with your own connection details:

```python
conn = psycopg2.connect(
    host="your-host",
    port=5432,
    dbname="your-database-name",
    user="your-username",
    password="your-password",
    sslmode="require"
)
```

You can use a free cloud database (for example [Neon](https://neon.tech)) or a local PostgreSQL installation. For a local installation, `host` is usually `localhost` and `sslmode` can be removed.

> **Tip:** Never commit real passwords to a public repository. Store them in environment variables or in a `.env` file listed in `.gitignore`.

## How It Works

The notebook is organized in steps:

1. **`Employee` class** – Defines the attributes `first_name`, `last_name`, `age`, `salary` and `department`, and a `display_info()` method that returns a formatted description.
2. **Read the CSV** – Loads `pythondata.csv` into a pandas DataFrame with `pd.read_csv()`.
3. **Preview** – Uses `head()` and `columns` to inspect the data.
4. **Clean the data** – Checks missing values with `isnull().sum()`, fills missing `age` and `salary` values with the column mean using `fillna()`, and converts `age` to `int`.
5. **Statistics** – Uses `describe()` on the `age` and `salary` columns.
6. **Create objects** – Loops through the DataFrame with `iterrows()` and creates an `Employee` object for each row.
7. **Display** – Prints every employee using `display_info()`.
8. **Filter** – Selects employees older than 30 with `df[df['age'] > 30]`.
9. **Extend the class** – Adds `give_bonus(amount)` and `change_department(new_department)`, and tests them on an employee.
10. **Save to PostgreSQL** – Creates the `employees` table, clears old rows, inserts all records and verifies the result with `SELECT COUNT(*)`.

### Example

```python
emp = employees[0]
print(emp.display_info())

emp.give_bonus(500)
emp.change_department("Management")
print(emp.display_info())
```

### Database Schema

```sql
CREATE TABLE IF NOT EXISTS employees (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    age INTEGER,
    salary FLOAT,
    department VARCHAR(100)
);
```

> **Note:** The notebook runs `DELETE FROM employees;` before inserting, so running it multiple times does not create duplicate rows.

## Using the Database Backup

The file `database.sql` is a plain-text PostgreSQL backup containing the `employees` table and its data. To restore it into a local database:

```bash
createdb -U postgres employees_db
psql -U postgres -d employees_db -f database.sql
```

Alternatively, open the file in the **Query Tool** of pgAdmin (connected to an empty database) and run it.

## What I Learned

- Reading, cleaning and analyzing data with pandas
- Handling missing values and converting data types
- Object-oriented programming in Python (classes, attributes, methods)
- Connecting Python to a PostgreSQL database and running SQL queries with parameters
- Creating and restoring a database backup with pgAdmin
