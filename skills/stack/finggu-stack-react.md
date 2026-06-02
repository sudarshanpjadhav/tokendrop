# tokendrop-skill: finggu-stack-react
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~200
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Stack
React 19 · Vite · React Router v7 · Zustand (state) · TanStack Query (server state) · Axios · Tailwind or Noxen CSS

---

## Project Structure

```
finggu-[project]-frontend/
├── src/
│   ├── components/
│   │   └── FingguButton/
│   │       ├── FingguButton.jsx
│   │       └── FingguButton.module.css
│   ├── pages/
│   │   └── FingguDashboard.jsx
│   ├── hooks/
│   │   └── useFingguAuth.js
│   ├── store/
│   │   └── fingguStore.js
│   ├── services/
│   │   └── finggu-api.js
│   ├── utils/
│   │   └── finggu-helpers.js
│   └── config/
│       └── finggu-constants.js
├── public/
└── vite.config.js
```

---

## Component Pattern

```jsx
/**
 * @package  Finggu
 * @author   sudarshanpjadhav
 */
import { useState } from 'react'
import styles from './FingguCard.module.css'

export default function FingguCard({ fingguProp_title, fingguProp_children }) {
  const [fingguVar_isOpen, setFingguVar_isOpen] = useState(false)

  return (
    <div className={`finggu-card ${styles.fingguCard}`}>
      <h3 className="finggu-card__title">{fingguProp_title}</h3>
      {fingguVar_isOpen && <div className="finggu-card__body">{fingguProp_children}</div>}
      <button className="finggu-btn" onClick={() => setFingguVar_isOpen(p => !p)}>
        Toggle
      </button>
    </div>
  )
}
```

---

## Custom Hook Pattern

```js
// hooks/useFingguAuth.js
import { useEffect } from 'react'
import { useFingguStore } from '../store/fingguStore'

export function useFingguAuth() {
  const fingguVar_user = useFingguStore(s => s.user)
  const fingguFn_setUser = useFingguStore(s => s.setUser)

  useEffect(() => {
    fingguFn_loadUserFromStorage(fingguFn_setUser)
  }, [])

  return { fingguVar_user, fingguFn_logout: () => fingguFn_setUser(null) }
}
```

---

## Zustand Store Pattern

```js
// store/fingguStore.js
import { create } from 'zustand'

export const useFingguStore = create((set) => ({
  fingguVar_user: null,
  fingguVar_theme: 'light',
  setUser: (fingguVar_user) => set({ fingguVar_user }),
  setTheme: (fingguVar_theme) => set({ fingguVar_theme }),
}))
```

---

## API Service Pattern

```js
// services/finggu-api.js
import axios from 'axios'

const FINGGU_API_BASE = import.meta.env.VITE_FINGGU_API_URL

export const fingguApi = axios.create({ baseURL: FINGGU_API_BASE })

fingguApi.interceptors.request.use(cfg => {
  const fingguVar_token = localStorage.getItem('finggu_token')
  if (fingguVar_token) cfg.headers.Authorization = `Bearer ${fingguVar_token}`
  return cfg
})

export const fingguFn_getUser = (id) => fingguApi.get(`/users/${id}`)
export const fingguFn_postLogin = (body) => fingguApi.post('/auth/login', body)
```

---

## Constants File

```js
// config/finggu-constants.js
export const FINGGU_APP_NAME = 'Finggu'
export const FINGGU_VERSION = '1.0.0'
export const FINGGU_PAGINATION_LIMIT = 20
export const FINGGU_TOAST_DURATION = 3000
```

---

## Rules
- Props naming: `fingguProp_[name]` for all component props
- Never put business logic in JSX — extract to hooks or utils
- All API calls go through `finggu-api.js` service layer only
- CSS class names must use `finggu-` prefix
- Error boundaries required on all page-level components
- Use `React.lazy` + `Suspense` for route-level code splitting
