OWASP stands for Open Web Application Security Project.
**“OWASP is a global nonprofit organization focused on improving the security of software. It provides free resources, tools, and best practices for developers and security professionals to identify, prevent, and fix vulnerabilities in web applications.

One of its most well-known contributions is the ‘OWASP Top 10,’ which is a list of the 10 most critical web application security risks, like SQL Injection, Cross-Site Scripting (XSS), and Broken Authentication. It serves as a guideline to help developers and security teams secure applications against common threats.”**

DVWA (Damn Vulnerable Web Application)

1. What is DVWA?

    DVWA is an intentionally vulnerable web application designed for learning web security.
    - Key points:
      - Written in PHP and uses MySQL / MariaDB for data storage.
      - Allows users to practice common web vulnerabilities safely.
      - Great for beginners/intermediate students to understand attacks without harming real systems.
      - Supports various security levels: low, medium, high, impossible.

2. Purpose of DVWA

    DVWA exists for educational and training purposes:
    - Learning by doing: Instead of just reading about vulnerabilities, you actually exploit them.
    - Safe environment: You won’t damage live systems or databases.
    - Understanding OWASP Top 10: DVWA has examples of multiple vulnerabilities like:
      - SQL Injection (SQLi) 
      - Cross-Site Scripting (XSS) 
      - CSRF (Cross-Site Request Forgery) 
      - File Inclusion
      - Command Execution
      - Weak passwords / authentication flaws
    - Mitigation testing: You can see how security mechanisms (like prepared statements, input validation, and headers) stop attacks.

3. DVWA Structure

DVWA’s file structure is very organized. Here’s a simplified overview:

/var/www/html/DVWA/
│
├── config/                 # Configuration files (DB credentials, etc.)
│   ├── config.inc.php      # Active configuration
│   └── config.inc.php.dist # Template file
│
├── hackable/               # Pages containing vulnerabilities
│   ├── sqli/               # SQL Injection examples
│   ├── xss/                # Cross-site scripting
│   ├── csrf/               # CSRF examples
│   └── file_inclusion/     # LFI / RFI examples
│
├── setup.php               # Database setup / initialization script
├── index.php               # DVWA homepage
├── login.php               # Login page
└── README.md               # Instructions

Why important:
    - Each vulnerability is isolated in a separate folder → safe and focused learning.
    - config/ connects DVWA to MariaDB. Without this, DVWA cannot run.      
    - setup.php ensures the database tables are created properly.

4. Security Levels in DVWA

DVWA allows you to change security settings:

Level	Behavior
    - Low	No input filtering → attacks work easily (good for learning)
    - Medium	Some input validation & escaping → attacks need tweaking
    - High Proper security → attacks harder or blocked
    - Impossible Security cannot be bypassed → shows how a fully secure system behaves

5. Example Vulnerabilities in DVWA

    - SQL Injection (SQLi)
       - User input is directly added into SQL queries.
       - Example: SELECT * FROM users WHERE id='$id';
       - Vulnerable if $id comes from GET/POST without validation.
       - Attackers can retrieve usernames/passwords.
    - Cross-Site Scripting (XSS)
       - Injecting malicious scripts in forms/comments.
       - Can steal cookies or redirect users.
    - CSRF (Cross-Site Request Forgery)
       - Tricks logged-in users into performing actions without consent.
       - DVWA demonstrates this by changing passwords via hidden forms.
    - File Inclusion
       - LFI/RFI allows reading or executing server files.
       - Commonly used to steal configs or run malicious code.

6. How DVWA Works
      
    - User accesses a page (e.g., SQLi page).
    - The page takes user input (like id) and builds an SQL query.
    - If the input isn’t sanitized:
      - SQLi can extract data from the DB.
      - XSS can inject scripts.
    - By switching security levels:
      - Low → vulnerable
      - High → input is filtered / prepared statements are used
    - You can test attacks, then implement fixes → demonstrates vulnerability → mitigation.

STEP 1: SQL Injection  

1) What is SQL Injection (SQLi)?

- Definition:
  - SQL Injection is a vulnerability where an attacker can manipulate a web application’s SQL queries by injecting malicious input.
- Why it happens:
  - When user input is directly inserted into SQL queries without validation or escaping.
  - The database executes whatever the input modifies the query to do.
- Real-world example (without DVWA):
  - $id = $_GET['id'];
  - $query = "SELECT * FROM users WHERE user_id = '$id'";
  - If a user enters 1 → query works normally.
  - If a user enters 1 OR 1=1 → query becomes:
  - SELECT * FROM users WHERE user_id = '1' OR 1=1;
  - 1=1 is always true → returns all rows in the database.
- Danger:
  - Attackers can read sensitive data, modify/delete records, or even compromise the system.

2) Why DVWA is Safe for SQLi

- Localhost Only:
  - DVWA runs on 127.0.0.1 (your own machine).
  - No internet exposure → only you can access it.
- Dedicated Database User:
  - CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'dvwa_pass';
  - GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
  - SQLi can only affect the dvwa database.
  - Root DB and OS files are safe. 
  - Resettable Environment:    
  - setup.php → Create / Reset Database resets the database anytime.

3) DVWA Security Levels

    - Low: No filtering → SQLi works easily.
    - Medium/High/Impossible:
    - Adds input validation, escaping, or prepared statements.
    - Helps demonstrate how proper coding prevents attacks.
    - Why Low:
    - It allows you to see the vulnerability in action.
    - Learning first in low helps understand the mechanics of SQLi.

4) Performing SQLi in DVWA

    - Steps:
    - Go to the SQL Injection page:
    - http://127.0.0.1/DVWA/vulnerabilities/sqli/
    - Input box ID takes user input. Example: 1 OR 1=1
    - The PHP page executes the query:
    - $query = "SELECT first_name, last_name, user_id FROM users WHERE user_id = '$id'";
    - Our input manipulates the query to:
    - SELECT first_name, last_name, user_id FROM users WHERE user_id = '1' OR 1=1;
    - OR 1=1 always evaluates to true → all users are returned.
    - Output: You see multiple usernames and passwords on the page.
    
5) Demonstrating the Fix (Prepared Statements)

    - Prepared statements separate query structure from user input:
    - $stmt = $db->prepare("SELECT * FROM users WHERE user_id = ?");
    - $stmt->bind_param("i", $id);
    - $stmt->execute();
    - ? is a placeholder; $id is bound safely.
    - Even if the user enters '1 OR 1=1', the database treats it as a literal value, not part of SQL.
    - It only provides the first id as output.
    - Result: Injection fails → only correct user_id rows are returned.

STEP 2- Cross-Site Scripting (XSS)

What is XSS?
- Cross-Site Scripting (XSS) is a class of web vulnerability where an attacker is able to inject client-side scripts (usually JavaScript) into pages viewed by other users. When the victim’s browser executes the injected script, the attacker can do things like steal session cookies, manipulate the DOM, perform actions as the user, show fake UI, etc.
- Two common types you’ll test
  - Stored (Persistent) XSS
    - How it works: Attacker submits malicious script into a site input (comment, profile, message) that the server stores in the database. When any user later views that stored data, the script runs in their browser.
    - Impact: Broad — any user who views the page can be affected; can be used to steal cookies, perform actions, pivot.
    - Consequences of Stored XSS include:
        - Redirecting users to malicious sites
        - Stealing cookies or session tokens
        - Defacing the website
        - Performing actions on behalf of other users
    - Commands
       - Open DVWA and navigate to Stored XSS page:
       - Enter malicious payload in the Guestbook form:
         - Name: Bhagyalaxmi
         - Message: <img src=x onerror=alert(2)>
         - Click Sign Guestbook
    - Output
      - The page reloads and shows the guestbook entries.
      - A popup with 2 appears — this confirms that the browser executed the injected script.
      - When any user loads that page, the malicious code executes in their browser.
      - In our lab, the popup 2 is just a safe demonstration — it proves that the input was executed as code instead of being treated as plain text.
      - In real life, a hacker could replace alert(2) with something harmful: stealing cookies, logging keystrokes, redirecting the user, etc.
        
  - Reflected (Non-persistent) XSS
    - How it works: The server reflects attacker-controlled input immediately in a response (e.g., search term shown in results or a name parameter echoed back), without storing it. The attacker crafts a URL containing the payload and convinces a victim to click it. 
    - Impact: Targeted — requires tricking a victim (phishing link), but still very dangerous.
    - Consequences include:
      - Stealing cookies/session tokens
      - Phishing attacks
      - Redirection to malicious websites
    - Commands:
      - Open DVWA and navigate to Reflected XSS page
      - Your name: <script>alert('Reflected XSS Mallicious Content')</script>
    - Output:
      - The input was reflected directly in the page response.
      - The browser executed it as JavaScript, producing the alert popup.
      - This demonstrates a Reflected XSS vulnerability, showing that any user who clicks the link could trigger the script.
      - Attackers exploit it via malicious links in emails, social media, or phishing pages.
        
- Why it happens
  - The app outputs user data (from query string, form fields, DB) into HTML without escaping/encoding it.
  - Browsers parse the HTML and run any script tags, event handlers, onerror, etc.

- How to prevent XSS (two main techniques we’ll demonstrate)
  - Input Sanitization / input validation (server-side):
    - Checking and restricting what users can input before it goes to the database or page.Restrict what users can input. Example: allow only letters/numbers for a name field. Remove or encode HTML special characters (<, >, &, ", '). This ensures scripts are displayed as text, not executed.Malicious users often inject scripts via form fields. If we restrict inputs to safe characters, scripts cannot execute.Displayed as text, not executed.
  - Output Encoding / Escaping  
    -  Even if user input contains HTML or scripts, encode it before rendering on the page. Prevents browser from interpreting input as executable code. Use htmlspecialchars (PHP) , HTMLEncode() (other languages),or template auto-escaping to render user data as text, not as HTML. Prefer the output encoding model (escape on output). Encode user data before rendering it on a page.Any injected script is shown as text, popup doesn’t appear.
    
  - Content Security Policy (CSP):
     - A browser-enforced rule that restricts what resources (scripts, images, CSS) can run on your site. Browser policy delivered via headers that restrict which scripts can execute (e.g., only scripts from the same origin, disallow inline scripts). CSP is defense-in-depth — it mitigates impact even if something slips through. Even if a malicious script is injected, CSP can block execution.

