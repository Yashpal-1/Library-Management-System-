# 📖 Viva Notes — Library Management System

---

## 1. Request Flow

When a user interacts with the system, the request travels through these layers:

```
Browser (HTML form / link)
      ↓  HTTP Request
Servlet (e.g. BookServlet.java)
      ↓  Calls business logic
Service (e.g. BookService.java)
      ↓  Reads / writes
ArrayList<Book> in DataStore.java  ← in-memory "database"
      ↓  Data set on request attributes
JSP Page (e.g. books.jsp) renders the HTML response
      ↓  HTTP Response
Browser displays the updated page
```

### Detailed Example — Adding a Book

1. User fills the **Add Book form** in `books.jsp` and clicks Submit
2. Browser sends `POST /books?action=add` with form data
3. **BookServlet.doPost()** reads the parameters, validates them server-side
4. If valid → calls `BookService.addBook(...)` 
5. **BookService** creates a new `Book` object, assigns the next auto-ID, adds it to `DataStore.books`
6. Servlet sets a flash message in the session and redirects back to `/books`
7. **BookServlet.doGet()** loads the updated list from `DataStore.books` and sets it as a request attribute
8. **books.jsp** renders the table using JSTL `<c:forEach>` over the list

---

## 2. Why ArrayList Instead of a Database?

### Why we used ArrayList

| Reason | Explanation |
|--------|-------------|
| **Simplicity** | No SQL to write, no driver to configure |
| **Beginner-friendly** | Plain Java — loops, objects, getters |
| **Zero setup** | Works out of the box with no extra software |
| **Fast for demos** | Pre-seeded data is available the moment the server starts |

### Limitations of ArrayList

| Limitation | Impact |
|-----------|--------|
| **Data lost on restart** | Every time you stop the server, all books/members/issues disappear |
| **No concurrent access safety** | If two people submit forms at exactly the same moment, data could get corrupted |
| **No querying** | You cannot filter or sort efficiently with large amounts of data |
| **No persistence** | You cannot back up data or recover it after a crash |
| **Memory only** | All data lives in RAM; if the machine runs out of RAM, everything is lost |

---

## 3. Fine Calculation Logic

```java
// Free period = 14 days
// Fine rate   = Rs. 2 per extra day

long daysHeld = ChronoUnit.DAYS.between(issueDate, today);

if (daysHeld > 14) {
    fine = (daysHeld - 14) * 2.0;
} else {
    fine = 0.0;
}
```

**Example:** Book issued on Day 1, returned on Day 20.
- Days held = 20
- Overdue days = 20 − 14 = 6
- Fine = 6 × Rs. 2 = **Rs. 12**

---

## 4. Session-Based Authentication

```
User submits /login with username=admin, password=admin123
          ↓
LoginServlet checks credentials (hardcoded)
          ↓ (if correct)
session.setAttribute("loggedIn", true)   ← creates a server-side session
          ↓
Browser receives a JSESSIONID cookie
          ↓
Every subsequent request sends this cookie
          ↓
Each servlet checks: session.getAttribute("loggedIn") != null
If missing → redirect to /login.html
```

---

## 5. Five Likely Viva Questions and Answers

---

**Q1. What is a Servlet? How is it different from a regular Java class?**

**A:** A Servlet is a special Java class that handles HTTP requests. It extends `HttpServlet` and overrides `doGet()` / `doPost()` methods. Unlike a regular Java class, a servlet is managed by a web container (like Jetty or Tomcat) — the container creates the servlet instance, calls its methods when requests arrive, and destroys it when the server stops. A regular Java class has no knowledge of HTTP or web concepts.

---

**Q2. Why did you use `static` ArrayLists in DataStore?**

**A:** We use `static` so that **all servlet instances share the same single copy** of the data. Servlets can be instantiated multiple times by the web container. If the lists were instance variables, each servlet instance would have its own empty list and data would never be shared. `static` makes the lists belong to the class itself — so every servlet reads and writes to the same list.

---

**Q3. What happens to the data when the server restarts?**

**A:** All data is lost. Since we store everything in `ArrayList` objects in Java's heap memory (RAM), when the JVM shuts down, the memory is released. On next startup, the `static` initializer in `DataStore` runs again and re-creates only the pre-seeded sample data. This is the biggest limitation of in-memory storage vs. a database like MySQL.

---

**Q4. How does the fine calculation work? What is the formula?**

**A:** We allow a **14-day free borrowing period**. If a member returns the book within 14 days, the fine is Rs. 0. For every day beyond 14 days, we charge **Rs. 2 per day**.

Formula: `fine = (daysHeld − 14) × 2`

We use Java's `ChronoUnit.DAYS.between(issueDate, returnDate)` to count the days accurately, including across months.

---

**Q5. What is the purpose of JSTL and EL in JSP? Give an example.**

**A:** 
- **EL (Expression Language)** allows you to display Java objects in JSP without writing Java code. Example: `${book.title}` automatically calls `book.getTitle()`.
- **JSTL (JSP Standard Tag Library)** provides tags to replace Java scriptlets. Example: `<c:forEach var="book" items="${bookList}">` iterates over an ArrayList without any `for` loop Java code in the JSP.

Together they keep JSP files clean HTML templates rather than messy Java+HTML mixes.

---

## 6. Optional Improvements

| Improvement | How to Implement |
|------------|-----------------|
| **Save data to a file** | Use `ObjectOutputStream` to serialize the ArrayLists to a `.dat` file on server shutdown (via `ServletContextListener`) and reload on startup |
| **Upgrade to MySQL** | Add MySQL JDBC driver to `pom.xml`, replace `DataStore.books` with SQL `INSERT/SELECT/UPDATE/DELETE` queries using `PreparedStatement` |
| **Add pagination** | In the servlet, calculate start/end indexes based on a `page` parameter and pass a sub-list to JSP |
| **Member login** | Add a separate `members` login table, create `MemberLoginServlet`, use separate session attribute |
| **Email notifications** | Use JavaMail API to send overdue reminders — trigger when fine is calculated |
| **Export to PDF/Excel** | Use Apache POI (Excel) or iText (PDF) to generate downloadable reports |
| **Input sanitization** | Use OWASP Java HTML Sanitizer to prevent XSS attacks on user inputs |
| **Password hashing** | Store hashed passwords using `BCrypt` instead of plaintext comparison |
