---
title: 'How to Build and Package a VS Code Extension in TypeScript'
description: 'A step-by-step guide to building a practical VS Code extension that wraps selected code in a try-catch block, debugging it, and packaging it into a .vsix file.'
pubDate: 2026-11-11
categories: ['VSCode', 'TypeScript', 'Tutorial']
language: 'en'
---

Building a VS Code extension is easier than you might think. In this guide, we'll build a practical extension that takes any selected code in TypeScript and wraps it in a `try-catch` block via the command palette.

---

### 1. Project Setup & Manifest (`package.json`)

Create a new directory for your extension and initialize `package.json`. The `package.json` file serves as the manifest for your extension, defining its commands, entry point (`main`), and engine compatibility.

```json
{
  "name": "wrap-try-catch",
  "displayName": "Wrap in Try-Catch",
  "version": "0.1.0",
  "engines": {
    "vscode": "^1.85.0"
  },
  "categories": ["Other"],
  "activationEvents": [],
  "main": "./out/extension.js",
  "contributes": {
    "commands": [
      {
        "command": "wrapSelection.tryCatch",
        "title": "Wrap Selection in Try-Catch"
      }
    ]
  },
  "scripts": {
    "compile": "tsc -p ."
  },
  "devDependencies": {
    "@types/node": "^18.x",
    "@types/vscode": "^1.85.0",
    "typescript": "^5.x"
  }
}
```

Install the dependencies:
```bash
npm install
```

---

### 2. Writing the Extension (`src/extension.ts`)

Create a `src/extension.ts` file. Every extension must export an `activate` function which registers commands and handles subscriptions.

```typescript
import * as vscode from 'vscode';

export function activate(context: vscode.ExtensionContext) {
    let disposable = vscode.commands.registerCommand('wrapSelection.tryCatch', async () => {
        const editor = vscode.window.activeTextEditor;
        if (!editor) {
            vscode.window.showInformationMessage('No active editor found.');
            return;
        }

        const selection = editor.selection;
        const selectedText = editor.document.getText(selection);

        if (!selectedText || selectedText.trim().length === 0) {
            vscode.window.showWarningMessage('No text selected.');
            return;
        }

        const lines = selectedText.split('\n');
        // Calculate indentation of the first line
        const firstLine = editor.document.lineAt(selection.start.line);
        const match = firstLine.text.match(/^(\s*)/);
        const indent = match ? match[1] : '';

        // Indent each line of the selected code
        const indentedLines = lines.map(line => line.length > 0 ? indent + '    ' + line : line);
        
        const wrappedCode = [
            indent + 'try {',
            ...indentedLines,
            indent + '} catch (error) {',
            indent + '    console.error(error);',
            indent + '}'
        ].join('\n');

        await editor.edit(editBuilder => {
            editBuilder.replace(selection, wrappedCode);
        });
    });

    context.subscriptions.push(disposable);
}

export function deactivate() {}
```

---

### 3. Compiling and Debugging Locally

1. Compile the TypeScript code to JavaScript:
   ```bash
   npm run compile
   ```
2. Open the project in VS Code.
3. Set a breakpoint in `src/extension.ts` inside your command handler.
4. Press **F5** to launch a new **Extension Development Host** window.
5. In the new window, open a file, select some code, press `Ctrl+Shift+P` (or `Cmd+Shift+P`), and run **Wrap Selection in Try-Catch**.

---

### 4. Packaging into a `.vsix` File

To share your extension or install it manually (in VS Code or forked editors like Cursor and Windsurf), bundle it into a `.vsix` package using `@vscode/vsce`:

```bash
npx vsce package --allow-missing-repository
```

This generates a `.vsix` file in your root directory (e.g., `wrap-try-catch-0.1.0.vsix`).

To install it:
1. Open the **Extensions** view in VS Code (`Ctrl+Shift+X`).
2. Click the `...` (Views and More Actions) menu at the top right of the Extensions panel.
3. Select **Install from VSIX...** and choose your `.vsix` file.

---

### Reference

- [Full Tutorial | Build A VSCode Extension](https://www.youtube.com/watch?v=4vLfXL0Rm38) by James Charlesworth
