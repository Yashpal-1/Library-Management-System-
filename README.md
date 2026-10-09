# 📚 Library Management System

A beginner-friendly Java web application for managing library books, members, and book issue/return records.

---

## 🔐 Login Credentials

| Field    | Value      |
|----------|------------|
| Username | `admin`    |
| Password | `admin123` |

---

## 🚀 How to Run (One Command)

### Prerequisites
- Java 17 or later installed
- Internet access (first run downloads Maven dependencies ~30 MB)

### Steps

1. **Open a terminal** and navigate to the project folder:
   ```
   cd library-management
   ```

2. **Run the app** using the Maven wrapper (downloads Maven automatically):
   ```
   C:\Users\yashp\.gemini\antigravity\scratch\maven\apache-maven-3.9.6\bin\mvn.cmd jetty:run
   ```
   > **Or** if Maven is installed globally: `mvn jetty:run`

3. **Open your browser** and go to:
   ```
   http://localhost:8080/library
   ```

4. **Login** with `admin` / `admin123`

5. To **stop** the server, press `Ctrl + C` in the terminal.

---

## 📂 Project Structure

```
library-management/
├── pom.xml                          ← Maven build file + Jetty plugin
├── README.md
├── VIVA_NOTES.md
└── src/main/
    ├── java/com/library/
    │   ├── model/
    │   │   ├── Book.java            ← Book entity (id, title, author, isbn, genre, copies)
    │   │   ├── Member.java          ← Member entity (id, name, email, phone, address)
    │   │   └── IssueRecord.java     ← Issue/return record with fine field
    │   ├── data/
    │   │   └── DataStore.java       ← Static ArrayLists + ID counters + seed data
    │   ├── service/
    │   │   ├── BookService.java     ← CRUD + search for books
    │   │   ├── MemberService.java   ← CRUD for members
    │   │   └── IssueService.java    ← Issue/return + fine calculation
    │   └── servlet/
    │       ├── LoginServlet.java    ← POST /login (session creation)
    │       ├── LogoutServlet.java   ← GET  /logout (session invalidation)
    │       ├── DashboardServlet.java← GET  /dashboard (stats)
    │       ├── BookServlet.java     ← /books (list/add/edit/delete/search)
    │       ├── MemberServlet.java   ← /members (list/add/edit/delete)
    │       └── IssueServlet.java    ← /issues (issue/return/overdue)
    └── webapp/
        ├── WEB-INF/web.xml
        ├── css/style.css            ← Shared stylesheet (no Bootstrap)
        ├── login.html
        ├── dashboard.jsp
        ├── books.jsp
        ├── members.jsp
        └── issues.jsp
```

---

## ✨ Features

| Feature | Details |
|---------|---------|
| Admin Login/Logout | Session-based; hardcoded credentials |
| Books | Add, Edit, Delete, Search (title/author/ID) |
| Members | Add, Edit, Delete, View all |
| Issue Book | Choose member + book; reduces available copies |
| Return Book | Auto-calculates fine (Rs. 2/day after 14 days) |
| Dashboard | Shows totals: books, copies, members, issued, overdue |
| Overdue Table | Lists all overdue books with running fine |

---

## 📦 Pre-loaded Sample Data

**Books (6):** The Great Gatsby, To Kill a Mockingbird, 1984, The Alchemist, Harry Potter, Clean Code

**Members (3):** Aisha Patel, Rohan Sharma, Priya Nair

**Sample Issue:** "1984" issued to Aisha Patel 20 days ago → Rs. 12 fine visible immediately

---

## ⚠️ Important Note

All data is stored **in memory only**. It will be **lost when the server restarts**. This is intentional for a beginner project. See `VIVA_NOTES.md` for how to upgrade to a database later.
