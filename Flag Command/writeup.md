# Flag Command - HTB Challenge

## 🧠 Overview
This challenge was a browser-based game where the user progresses through a command-line style interface by selecting correct answers.

No traditional exploitation or scanning tools were required. The challenge relied on client-side JavaScript analysis and API behavior understanding.

---

## 🔍 Initial Approach

At first, a full port scan with Nmap was attempted, but it was not relevant since the challenge exposed a web interface directly on a given port.

The application presented a CLI-like game where each correct answer advanced to the next stage.

---

## 🌐 Web Analysis

Upon inspecting the browser source code, multiple JavaScript files were found:

- main.js
- commands.js
- game.js

These files contained most of the game logic.

---

## 🧠 Key Discovery

Inside `main.js`, an API endpoint was discovered:

```bash
fetch('/api/options')
```

This endpoint returned the possible answers depending on the current question state.

---

## 💣 Exploitation

While progressing through the game, in the last quest, it became clear that:

- All visible options were incorrect
- The correct answer was not displayed in the UI

However, the `/api/options` response contained a hidden value:

```json
{
  "secret": ["Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"]
}
```


This option was not shown in the UI but was accepted by the backend.

By submitting this hidden option at the final stage, the game allowed progression and granted access.

---

## 🏁 Result

Using the hidden “secret” option bypassed the intended logic and completed the game, revealing the flag.

```
HTB{D3v3l0p3r_t00l5_4r3_b35t__t0015_wh4t_d0_y0u_Th1nk??}
```
