#### vscode-setting.json

```json
{
  "workbench.colorTheme": "Atom One Dark",
  "workbench.iconTheme": "material-icon-theme",
  "workbench.activityBar.visible": false,
  "editor.tabSize": 2,
  "vim.useSystemClipboard": true,
  "editor.minimap.enabled": false,
  "editor.linkedEditing": true,
  "editor.fontSize": 13,
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}

```

#### keybinding.json

```json
// Place your key bindings in this file to override the defaultsauto[]
[
  {
    "key": "cmd+k cmd+d",
    "command": "-editor.action.moveSelectionToNextFindMatch",
    "when": "editorFocus"
  },
  {
    "key": "cmd+k cmd+d",
    "command": "editor.action.formatDocument",
    "when": "editorHasDocumentFormattingProvider && editorTextFocus && !editorReadonly && !inCompositeEditor"
  },
  {
    "key": "shift+alt+f",
    "command": "-editor.action.formatDocument",
    "when": "editorHasDocumentFormattingProvider && editorTextFocus && !editorReadonly && !inCompositeEditor"
  }
]
```
