# 🛡️ XSS Vulnerability Fix

## Vulnerability Summary
**File:** `sample-app/index.html`  
**Line:** 280  
**Issue:** DOM-based Cross-Site Scripting (XSS)  
**Severity:** Medium  

## The Problem
The `tag` value is inserted directly into HTML without sanitization:

```javascript
// VULNERABLE CODE (line 280)
<span class="tag tag-${t.tag}">${t.tag}</span>
```

While the task text is properly escaped:
```javascript
// SECURE CODE (line 279) 
<span class="task-text">${escHtml(t.text)}</span>
```

## The Fix

Change line 280 in `sample-app/index.html` from:
```javascript
<span class="tag tag-${t.tag}">${t.tag}</span>
```

To:
```javascript
<span class="tag tag-${escHtml(t.tag)}">${escHtml(t.tag)}</span>
```

## Complete Fixed Code Block
```javascript
list.innerHTML = visible.map(t => `
  <div class="task-item ${t.done ? 'done' : ''}">
    <input type="checkbox" ${t.done ? 'checked' : ''} onchange="toggleDone(${t.id})" />
    <span class="task-text">${escHtml(t.text)}</span>
    <span class="tag tag-${escHtml(t.tag)}">${escHtml(t.tag)}</span>
    <button class="delete-btn" onclick="deleteTask(${t.id})" title="Delete">×</button>
  </div>
`).join('');
```

## Additional Security Enhancements

1. **Input Validation** - Add tag validation in the `addTask()` function:
```javascript
function addTask() {
  const input = document.getElementById('task-input');
  const text = input.value.trim();
  if (!text) { input.focus(); return; }

  // Ensure tag is from allowed list only
  const selectedTag = randomTag();
  const allowedTags = ['work', 'personal', 'urgent'];
  const safeTag = allowedTags.includes(selectedTag) ? selectedTag : 'work';

  tasks.unshift({ id: Date.now(), text, done: false, tag: safeTag });
  input.value = '';
  save();
  render();
  input.focus();
}
```

2. **Content Security Policy** - Add to HTML head:
```html
<meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline'; object-src 'none';">
```

## Testing the Fix
After applying the fix:
1. Run the exploit from `xss-exploit-demo.html`
2. The XSS payload should be rendered as harmless text instead of executing
3. The malicious script tags will appear as literal text in the tag field