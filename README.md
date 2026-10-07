# TOC DFA Equivalence Tester

> Theory of Computation (TOC) · Joint State / Product Automaton Equivalence Testing & Visualization

An interactive, mathematically rigorous web and desktop application designed for B.Tech Computer Science & Engineering (CSE) and Artificial Intelligence & Machine Learning (AIML) courses. It allows users to input two separate Deterministic Finite Automata (DFA A and DFA B) and systematically test whether they accept the exact same regular language ($L(A) = L(B)$) by constructing the reachable joint state product machine.

---

## 🌟 Key Features

1. **Dual DFA Transition Table Input**:
   - Easily add/remove states, symbols, start state, and final/accepting states.
   - Interactive transition table matrix $\delta(q, a)$ with real-time validation (deterministic completeness, duplicate checks, alphabet compatibility).
   - One-click alphabet synchronization if automata use disparate symbol sets.

2. **Joint State / Product Construction Machine**:
   - Computes Cartesian product state space $Q' = Q_A \times Q_B$.
   - For every joint pair $(q_A, q_B)$ and symbol $a \in \Sigma$, calculates:
     $$\delta'((q_A, q_B), a) = (\delta_A(q_A, a), \delta_B(q_B, a))$$
   - Enforces the symmetric difference accepting criteria:
     $$F_{\text{product}} = (F_A \times (Q_B \setminus F_B)) \cup ((Q_A \setminus F_A) \times F_B)$$
   - Dynamically highlights reachable product states vs. unreachable states, with a filter to inspect all $|Q_A| \times |Q_B|$ states.

3. **Guaranteed Shortest Counterexample**:
   - Uses Breadth-First Search (BFS) over the product automaton starting from $(q_{0A}, q_{0B})$.
   - If a mismatch state is reached, BFS guarantees extraction of the **shortest distinguishing counterexample string** $w$ (including $\varepsilon$).
   - Displays parallel step-by-step state traces showing why $w \in L(A)$ and $w \notin L(B)$ (or vice versa).

4. **Interactive Automata State Graph Visualizer**:
   - Interactive SVG canvas with zoom, pan, and draggable state nodes.
   - Separate visualization views for:
     * DFA A Graph
     * DFA B Graph
     * Joint Product Automaton Graph
   - Double circles for accepting states, start state incoming indicator arrows, and glow-highlighted mismatch nodes.

5. **Step-by-Step String Simulator**:
   - Test custom strings (or $\varepsilon$) with simultaneous parallel execution across both automata.
   - Displays visited state sequences and final accept/reject verdicts.

6. **Viva Voce & Theory Mode**:
   - Formal mathematical formulation of $L(A) \Delta L(B) = \emptyset$.
   - Time complexity $O(|Q_A| \times |Q_B| \times |\Sigma|)$ and space complexity $O(|Q_A| \times |Q_B|)$.
   - Real-world applications in compiler lexical analysis and model checking.
   - Curated list of typical college viva questions with answers.

7. **JSON Import & Export**:
   - Export and import DFA configurations in standardized JSON format.

---

## 🚀 Quick Start (Local Web Application)

### Prerequisites
- Node.js (v18 or higher recommended)
- npm or yarn

### Installation
Clone or download the project folder, then run:

```bash
npm install
```

### Start Development Server
```bash
npm run dev
```
Open your browser at `http://localhost:3000`. The application is optimized for standard desktop displays (1366×768 and 1920×1080).

### Build for Production
To create an optimized production static bundle in the `dist/` directory:
```bash
npm run build
```

To preview the production build locally:
```bash
npm run preview
```

---

## 🖥️ Packaging as a Windows Desktop Application (.exe)

This application is built with React and Vite and can be packaged into a native Windows executable (`.exe`) using Electron and `electron-builder`.

### Step 1: Install Electron Dependencies
In your project directory, run:
```bash
npm install -D electron electron-builder concurrently wait-on
```

### Step 2: Add Electron Entry Script
Create a directory named `electron/` and add `main.js`:

```javascript
// electron/main.js
const { app, BrowserWindow } = require('electron');
const path = require('path');

function createWindow() {
  const win = new BrowserWindow({
    width: 1440,
    height: 900,
    minWidth: 1024,
    minHeight: 700,
    title: 'TOC DFA Equivalence Tester',
    webPreferences: {
      nodeIntegration: false,
      contextIsolation: true,
    },
  });

  const isDev = process.env.NODE_ENV === 'development';
  if (isDev) {
    win.loadURL('http://localhost:3000');
  } else {
    win.loadFile(path.join(__dirname, '../dist/index.html'));
  }
}

app.whenReady().then(createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit();
});
```

### Step 3: Add Scripts to `package.json`
Add the following commands to your `scripts` in `package.json`:
```json
"electron:dev": "concurrently \"npm run dev\" \"wait-on http://localhost:3000 && electron electron/main.js\"",
"electron:build": "npm run build && electron-builder --win"
```

Also add the build configuration:
```json
"build": {
  "appId": "com.toc.dfaequivalencetester",
  "productName": "TOC DFA Equivalence Tester",
  "directories": {
    "output": "release"
  },
  "files": [
    "dist/**/*",
    "electron/**/*"
  ],
  "win": {
    "target": "nsis"
  }
}
```

### Step 4: Run & Build Desktop App
- To test the desktop app in development:
  ```bash
  npm run electron:dev
  ```
- To build the standalone Windows `.exe` installer:
  ```bash
  npm run electron:build
  ```
The installer will be generated in the `release/` folder.

---

## 📐 Mathematical Formulation

1. **DFAs Defined**:
   $$M_A = (Q_A, \Sigma, \delta_A, q_{0A}, F_A)$$
   $$M_B = (Q_B, \Sigma, \delta_B, q_{0B}, F_B)$$

2. **Product Machine (Symmetric Difference)**:
   $$M_{\text{product}} = (Q_A \times Q_B, \Sigma, \delta_{\text{product}}, (q_{0A}, q_{0B}), F_{\text{product}})$$
   Where:
   $$\delta_{\text{product}}((q_A, q_B), a) = (\delta_A(q_A, a), \delta_B(q_B, a))$$
   $$F_{\text{product}} = (F_A \times (Q_B \setminus F_B)) \cup ((Q_A \setminus F_A) \times F_B)$$

3. **Equivalence Theorem**:
   $$L(M_A) = L(M_B) \iff L(M_{\text{product}}) = \emptyset$$

4. **Complexity**:
   - **Time Complexity**: $\mathcal{O}(|Q_A| \times |Q_B| \times |\Sigma|)$
   - **Space Complexity**: $\mathcal{O}(|Q_A| \times |Q_B|)$

---

## 📚 Project Attribution
- **Subject**: Theory of Computation / Formal Languages and Automata Theory (FLAT)
- **Course**: B.Tech CSE / AIML
