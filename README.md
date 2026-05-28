# shivam-resume
College table
# ================================
# COLLEGE MANAGEMENT SYSTEM
# SINGLE FILE PROJECT
# ================================

from flask import Flask, request, redirect, render_template_string
import sqlite3

app = Flask(__name__)

# ================================
# DATABASE CONNECTION
# ================================

def connect_db():
    conn = sqlite3.connect("college.db")
    conn.row_factory = sqlite3.Row
    return conn


# ================================
# CREATE TABLES
# ================================

conn = connect_db()

conn.execute("""
CREATE TABLE IF NOT EXISTS students(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    course TEXT
)
""")

conn.execute("""
CREATE TABLE IF NOT EXISTS teachers(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    subject TEXT
)
""")

conn.execute("""
CREATE TABLE IF NOT EXISTS attendance(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    student_name TEXT,
    status TEXT
)
""")

conn.execute("""
CREATE TABLE IF NOT EXISTS results(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    student_name TEXT,
    marks INTEGER
)
""")

conn.commit()
conn.close()


# ================================
# HOME PAGE
# ================================

HOME_HTML = """

<h1>College Management System</h1>

<hr>

<a href="/students">Student Management</a>

<br><br>

<a href="/teachers">Teacher Management</a>

<br><br>

<a href="/attendance">Attendance Management</a>

<br><br>

<a href="/results">Result Management</a>

"""

@app.route("/")
def home():
    return render_template_string(HOME_HTML)


# ================================
# STUDENTS
# ================================

STUDENT_HTML = """

<h2>Students</h2>

<form method="POST">

    Student Name:
    <input type="text" name="name">

    <br><br>

    Course:
    <input type="text" name="course">

    <br><br>

    <button type="submit">Add Student</button>

</form>

<hr>

<h3>Student List</h3>

{% for s in students %}

<p>
    {{ s['name'] }} - {{ s['course'] }}
</p>

{% endfor %}

<br>

<a href="/">Home</a>

"""

@app.route("/students", methods=["GET", "POST"])
def students():

    conn = connect_db()

    if request.method == "POST":

        name = request.form["name"]
        course = request.form["course"]

        conn.execute(
            "INSERT INTO students(name, course) VALUES (?, ?)",
            (name, course)
        )

        conn.commit()

    students = conn.execute(
        "SELECT * FROM students"
    ).fetchall()

    conn.close()

    return render_template_string(
        STUDENT_HTML,
        students=students
    )


# ================================
# TEACHERS
# ================================

TEACHER_HTML = """

<h2>Teachers</h2>

<form method="POST">

    Teacher Name:
    <input type="text" name="name">

    <br><br>

    Subject:
    <input type="text" name="subject">

    <br><br>

    <button type="submit">Add Teacher</button>

</form>

<hr>

<h3>Teacher List</h3>

{% for t in teachers %}

<p>
    {{ t['name'] }} - {{ t['subject'] }}
</p>

{% endfor %}

<br>

<a href="/">Home</a>

"""

@app.route("/teachers", methods=["GET", "POST"])
def teachers():

    conn = connect_db()

    if request.method == "POST":

        name = request.form["name"]
        subject = request.form["subject"]

        conn.execute(
            "INSERT INTO teachers(name, subject) VALUES (?, ?)",
            (name, subject)
        )

        conn.commit()

    teachers = conn.execute(
        "SELECT * FROM teachers"
    ).fetchall()

    conn.close()

    return render_template_string(
        TEACHER_HTML,
        teachers=teachers
    )


# ================================
# ATTENDANCE
# ================================

ATTENDANCE_HTML = """

<h2>Attendance</h2>

<form method="POST">

    Student:

    <select name="student">

        {% for s in students %}

        <option value="{{ s['name'] }}">
            {{ s['name'] }}
        </option>

        {% endfor %}

    </select>

    <br><br>

    Status:

    <select name="status">

        <option>Present</option>
        <option>Absent</option>

    </select>

    <br><br>

    <button type="submit">
        Save Attendance
    </button>

</form>

<hr>

<h3>Attendance Records</h3>

{% for a in attendance %}

<p>
    {{ a['student_name'] }} - {{ a['status'] }}
</p>

{% endfor %}

<br>

<a href="/">Home</a>

"""

@app.route("/attendance", methods=["GET", "POST"])
def attendance():

    conn = connect_db()

    students = conn.execute(
        "SELECT * FROM students"
    ).fetchall()

    if request.method == "POST":

        student = request.form["student"]
        status = request.form["status"]

        conn.execute(
            "INSERT INTO attendance(student_name, status) VALUES (?, ?)",
            (student, status)
        )

        conn.commit()

    attendance = conn.execute(
        "SELECT * FROM attendance"
    ).fetchall()

    conn.close()

    return render_template_string(
        ATTENDANCE_HTML,
        students=students,
        attendance=attendance
    )


# ================================
# RESULTS
# ================================

RESULT_HTML = """

<h2>Results</h2>

<form method="POST">

    Student:

    <select name="student">

        {% for s in students %}

        <option value="{{ s['name'] }}">
            {{ s['name'] }}
        </option>

        {% endfor %}

    </select>

    <br><br>

    Marks:

    <input type="number" name="marks">

    <br><br>

    <button type="submit">
        Save Result
    </button>

</form>

<hr>

<h3>Result Records</h3>

{% for r in results %}

<p>
    {{ r['student_name'] }} - {{ r['marks'] }}
</p>

{% endfor %}

<br>

<a href="/">Home</a>

"""

@app.route("/results", methods=["GET", "POST"])
def results():

    conn = connect_db()

    students = conn.execute(
        "SELECT * FROM students"
    ).fetchall()

    if request.method == "POST":

        student = request.form["student"]
        marks = request.form["marks"]

        conn.execute(
            "INSERT INTO results(student_name, marks) VALUES (?, ?)",
            (student, marks)
        )

        conn.commit()

    results = conn.execute(
        "SELECT * FROM results"
    ).fetchall()

    conn.close()

    return render_template_string(
        RESULT_HTML,
        students=students,
        results=results
    )


# ================================
# RUN APP
# ================================

if __name__ == "__main__":
    app.run(debug=True)