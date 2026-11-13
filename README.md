# SwasthyaSathi

SwasthyaSathi is a web application for community health workers to manage patient records, record field visits, review area health information, and access reports and AI-assisted tools.

## Technology stack

- **UI:** React 19 and plain CSS
- **Routing:** React Router 7
- **Build and development server:** Vite 8
- **Language/module format:** JavaScript with ES modules and JSX
- **Backend communication:** JSON REST API using the browser `fetch` API
- **Authentication:** Two-step password and OTP flow; the API token and signed-in user are stored in browser `localStorage`
- **Linting:** Oxlint

This repository contains the frontend only. Patient data, authentication, reports, notifications, AI features, and synchronization are provided by a separate backend API.

## Requirements

- Node.js compatible with Vite 8 (Node.js 20.19+ or 22.12+ recommended)
- npm
- Access to the SwasthyaSathi API for login and server-backed features

## Run locally

1. Install the dependencies:

   ```bash
   npm ci
   ```

2. Start the frontend development server:

   ```bash
   npm run dev
   ```

3. Open the URL printed by Vite (by default, <http://localhost:5173>).

The local login screen is pre-filled with the demo credentials `priya@demo.com` / `password123`. Submit them, then enter the demo OTP returned by the API; the application displays that OTP during the demo flow. A working API server and a valid demo account are required to complete login.

## API configuration

The API base URL is selected in [`src/utils/api.js`](./src/utils/api.js):

- When opened on `localhost` or `127.0.0.1`, the frontend calls `http://localhost:3001/api`. Run the backend locally on port `3001` to use local services.
- On other hostnames, the frontend calls `https://swasthyasathi-server.onrender.com/api`.

There is no `.env`-based API URL configuration in the current frontend. To point the application at a different backend, update the API base URL selection in `src/utils/api.js`. The backend must implement the endpoints used by the frontend, including authentication, patients, visits, AI, sync, notifications, and reports.

## Architecture and data flow

1. `src/main.jsx` mounts the React application into the root element and enables React Strict Mode.
2. `src/App.jsx` sets up browser routing and wraps the routes in `AuthProvider`. Protected pages redirect unauthenticated users to the login page.
3. `src/context/AuthContext.jsx` provides login, OTP verification, logout, current-user state, and browser online/offline status to the UI. After OTP verification, it stores the returned token and user in `localStorage`.
4. `src/components/Layout.jsx` provides the authenticated app shell, navigation, connection indicator, and nested page outlet.
5. Files under `src/pages/` implement the individual screens. They call the shared API client rather than calling the backend directly.
6. `src/utils/api.js` adds the stored token to API requests, handles JSON responses, and clears stored authentication data when it detects an invalid or expired token.

The app's sync screen reads pending/sync status from the backend and can request a sync push. This frontend repository does not include a local database or service worker; do not assume patient records can be created or retained offline unless that behavior is implemented and verified in the backend.

## Project structure

```text
.
├── index.html                 # HTML entry point and page metadata
├── public/                    # Static public assets
├── src/
│   ├── main.jsx               # React application bootstrap
│   ├── App.jsx                # Routes and protected-route handling
│   ├── assets/                # Images and bundled assets
│   ├── components/            # Shared UI, including the app layout
│   ├── context/               # Shared authentication and connectivity state
│   ├── pages/                 # Dashboard, patients, reports, and other screens
│   ├── styles/                # Global application styles
│   └── utils/                 # API client and icon components
├── package.json               # Dependencies and npm scripts
└── vite.config.js             # Vite and development-server configuration
```

## Main screens

- `/login` — password sign-in and OTP verification
- `/` — dashboard (authenticated)
- `/add-patient` — add a patient
- `/patients` and `/patients/:id` — patient list and patient profile
- `/area` — area overview
- `/field-mode` — field visit workflow
- `/ai-assistant` — AI-assisted patient tools
- `/sync` — backend synchronization status and pending records
- `/reports` — reports
- `/notifications` — notifications
- `/profile` — worker profile

All routes other than `/login` require an authenticated user.

## Useful commands

| Command | Description |
| --- | --- |
| `npm run dev` | Start the Vite development server |
| `npm run build` | Create the production bundle in `dist/` |
| `npm run preview` | Preview the production bundle locally |
| `npm run lint` | Run Oxlint |

## Production build

Run the checks and build:

```bash
npm run lint
npm run build
```

Deploy the generated `dist/` directory to a static web host configured to serve `index.html` for client-side routes. Configure the frontend to use the intended backend before deployment; non-local hostnames use the production API URL described above.
