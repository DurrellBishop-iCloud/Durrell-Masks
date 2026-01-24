# Mask App Development Notes

## Known Issues & Fixes

### JavaScript String Escaping in Alert Messages
**Problem:** App stuck on "Initializing..." or showing JS syntax error.

**Cause:** Double-escaped characters in alert strings:
```javascript
// WRONG - double escaped
alert('Can\'t be accessed.\\nNew line');

// CORRECT - single escaped
alert('Can\'t be accessed.\nNew line');
```

When using Claude to edit strings with quotes or newlines, watch for double-escaping (`\\'` instead of `\'`).

### MediaPipe Import Hanging
**Problem:** App stuck on "Loading..." forever.

**Cause:** The ES module import can be slow or hang. Using the direct .mjs file works better:
```javascript
// Use this format:
import('https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.8/vision_bundle.mjs')
```

### Module Script Not Executing
**Problem:** Inline script runs but module script doesn't start.

**Cause:** Any syntax error anywhere in the module prevents the entire module from executing. Use Node to check:
```bash
# Extract and check module script syntax
sed -n '/<script type="module"/,/<\/script>/p' index.html | sed '1d;$d' > /tmp/test.mjs
node --check /tmp/test.mjs
```

### Window Load Event Not Firing
**Problem:** Module code runs but window.load callback never executes.

**Cause:** With top-level await in modules, the DOM is already ready. Use an IIFE instead:
```javascript
// Instead of:
window.addEventListener('load', async () => { ... });

// Use:
(async () => { ... })();
```
