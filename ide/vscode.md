# Troubleshooting
## A shortcut doesn't work
### Go back
open prompt (cmd+shift+p), select **Developer: Toggle Keyboard Shortcuts Troubleshooting**, navigate to OUTPUT tab in the bottom window (where DEBUG CONSOLE tab is located).
The output will print debug messages while typing, like this:
```
2023-08-20 19:18:05.052 [info] [KeybindingService]: / Soft dispatching keyboard event
2023-08-20 19:18:05.052 [info] [KeybindingService]: \ Keyboard event cannot be dispatched
2023-08-20 19:18:05.052 [info] [KeybindingService]: / Received  keydown event - modifiers: [ctrl], code: ControlLeft, keyCode: 17, key: Control
2023-08-20 19:18:05.053 [info] [KeybindingService]: | Converted keydown event - modifiers: [ctrl], code: ControlLeft, keyCode: 5 ('Ctrl')
2023-08-20 19:18:05.053 [info] [KeybindingService]: \ Keyboard event cannot be dispatched in keydown phase.
2023-08-20 19:18:06.725 [info] [KeybindingService]: / Soft dispatching keyboard event
2023-08-20 19:18:06.725 [info] [KeybindingService]: | Resolving ctrl+[Minus]
2023-08-20 19:18:06.726 [info] [KeybindingService]: \ From 1 keybinding entries, matched workbench.action.navigateBack, when: canNavigateBack, source: built-in.
2023-08-20 19:18:06.726 [info] [KeybindingService]: / Received  keydown event - modifiers: [ctrl], code: Minus, keyCode: 189, key: -
2023-08-20 19:18:06.726 [info] [KeybindingService]: | Converted keydown event - modifiers: [ctrl], code: Minus, keyCode: 88 ('-')
2023-08-20 19:18:06.726 [info] [KeybindingService]: | Resolving ctrl+[Minus]
2023-08-20 19:18:06.727 [info] [KeybindingService]: \ From 1 keybinding entries, matched workbench.action.navigateBack, when: canNavigateBack, source: built-in.
2023-08-20 19:18:06.727 [info] [KeybindingService]: + Invoking command workbench.action.navigateBack.
2023-08-20 19:18:07.742 [info] [KeybindingService]: + Ignoring single modifier ctrl due to it being pressed together with other keys.
```
When the __Go back__ doesn't work the _Invoking command workbench.action.navigateBack._ doesn't show up. Possible reason for that is conflicting keybindings.  
Go to prompt again and turn off the **Developer: Toggle Keyboard Shortcuts Troubleshooting** by selecting it again.  

Now again from prompt select **Preferences: Open Keyboard Shortcuts**.   
Find __Go back__ try to assign keybinding again (ctrl+-). If there are more than 2 keybindings the prompt will show _2 existing commands have this keybinding_. This message is clickable - click on it and it will show which commands bind this shortcut. For me it was 
```json
{
  "key": "ctrl+-",
  "command": "workbench.action.navigateBack",
  "when": "canNavigateBack"
}
```
and
```json
{
  "key": "ctrl+-",
  "command": "workbench.action.quickInputBack",
  "when": "inQuickOpen"
}
```
I removed _quickInputBack_ and it fixed the problem
