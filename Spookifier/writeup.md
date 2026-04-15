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
HTB{REDACTED}
```

---

## 🧠 Takeaways

* SSTI can quickly escalate to full RCE
* Identifying the template engine is critical
* Not all payloads are universal — they depend on the engine
* Output exfiltration is just as important as command executionFlag Command - HTB Challenge

  ## 🧠 Overview

  This  challenge was a browser-based game where the user progresses through a  command-line style interface by selecting correct answers.

  ## No traditional exploitation or scanning tools were  required. The challenge relied on client-side JavaScript analysis and  API behavior understanding.

  ## 🔍 Initial Approach

  At  first, a full port scan with Nmap was attempted, but it was not  relevant since the challenge exposed a web interface directly on a given  port.

  ## The application presented a CLI-like game where each correct answer advanced to the next stage.

  ## 🌐 Web Analysis

  Upon inspecting the browser source code, multiple JavaScript files were found:

  * main.js
  * commands.js
  * game.js
    These files contained most of the game logic.

  ---

  ## 🧠 Key Discovery

  Inside `main.js`, an API endpoint was discovered:

  ```
  fetch('/api/options')
  ```

  ## This endpoint returned the possible answers depending on the current question state.

  ## 💣 Exploitation

  While progressing through the game, in the last quest, it became clear that:

  * All visible options were incorrect
  * The correct answer was not displayed in the UI
    However, the `/api/options` response contained a hidden value:

  ```
  {
    "secret": ["Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"]
  }
  ```

  This option was not shown in the UI but was accepted by the backend.

  ## By submitting this hidden option at the final stage, the game allowed progression and granted access.

  ## 🏁 Result

  Using the hidden “secret” option bypassed the intended logic and completed the game, revealing the flag.

  ```
  HTB{D3v3l0p3r_t00l5_4r3_b35t__t0015_wh4t_d0_y0u_Th1nk??}

  ```

![HTB Congrats](screenshots/congrats.png)

---
