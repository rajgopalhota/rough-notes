# **Solution: Use a Proxy in `vite.config.js` or `package.json`**
#### **If using Vite (Recommended)**
Add the following configuration in `vite.config.js`:
```js
import { defineConfig } from 'vite';

export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080', // Spring Boot backend
        changeOrigin: true,
        secure: false
      }
    }
  }
});
```
Now, when you call:
```js
fetch('/api/product')
```
It will automatically be forwarded to `http://localhost:8080/api/product`.

---

#### **If using Create React App (CRA)**
In your **React project's `package.json`**, add:
```json
"proxy": "http://localhost:8080"
```
Now, when you do:
```js
fetch('/api/product')
```
It will proxy to `http://localhost:8080/api/product`.

---

### **Why use a Proxy?**
✅ **Avoid hardcoding the backend URL**  
✅ **Easier to switch between local and production environments**  
✅ **Solves CORS issues automatically during development**  
