# Nginx

## Core Idea
Nginx is a web server with two distinct roles: **serve static files directly** or **act as a reverse proxy** forwarding requests to another application. "Exposing a port" is only one part of what it does.

## Production Flow for a Frontend
```
                    Internet
                       │
                       │ HTTP/HTTPS :443
                       ▼
                  ┌─────────┐
                  │  Nginx  │
                  └────┬────┘
                       │
             serves static files
                       │
                       ▼
              ┌─────────────────┐
              │ Frontend build  │
              │ dist/ or build/ │
              └─────────────────┘
```

1. **Build the frontend** — `npm run build` compiles React/Vue/Angular into static files:
```
dist/
├── index.html
├── assets/
│   ├── app.js
│   └── style.css
└── ...
```
Nginx does **not** run the React app — it's already compiled to static files.

2. **Serve those files** — put them on the server and point Nginx at the directory:
```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/my-app;
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

## What "Nginx exposes a port" Means
A local app on `localhost:3000` isn't reachable via your public URL. Nginx listens publicly on :80/:443 and forwards to it:

```
Internet
   │
   │ :443
   ▼
 Nginx
   │
   │ proxy_pass
   ▼
localhost:3000
   │
   ▼
Node.js application
```

This is a **reverse proxy**. For a purely static frontend, `proxy_pass` isn't needed at all.

## Frontend + Backend Behind One Nginx
```
                         Internet
                            │
                            ▼
                       ┌─────────┐
                       │  Nginx  │
                       │  :443   │
                       └────┬────┘
                            │
                 ┌──────────┴──────────┐
                 │                     │
              /api/*                   /*
                 │                     │
                 ▼                     ▼
          Backend :8080          Frontend files
```
```nginx
location / {
    root /var/www/my-app;
    try_files $uri $uri/ /index.html;
}
location /api/ {
    proxy_pass http://localhost:8080;
}
```

`https://example.com/` → React files; `https://example.com/api/users` → backend :8080.

## Why Use Nginx?
Capabilities: static file serving · reverse proxying · HTTPS/TLS termination · load balancing · compression · caching · routing · connection handling · security filtering.

Instead of exposing every service directly (`:3000`, `:8080`, `:5000`, `:9000`), all traffic goes through one public entry point:
```
Internet
   │
:443
   │
Nginx
/   |   \
frontend backend service
```

## Key Distinction
> Nginx can either **serve your frontend files directly**, or **forward requests to another application** (reverse proxy). Two different roles.

## Suggested Learning Order
1. What is a port?
2. What happens when you visit `https://example.com`?
3. What is a web server?
4. Nginx static file serving
5. Nginx `server` and `location` blocks
6. Reverse proxy and `proxy_pass`
7. Frontend + backend behind one Nginx
8. DNS → Nginx → application flow
9. HTTPS / TLS termination
10. Caching, compression, load balancing

## Revision Questions
1. Does Nginx run a React app? → No — it serves the compiled static build.
2. What is a reverse proxy? → Nginx forwards requests to another application (e.g. backend :8080).
3. Which config serves frontend vs backend? → `location /` + `try_files` serves files; `location /api/` + `proxy_pass` forwards.
4. What can Nginx handle? → Static files, reverse proxy, TLS termination, load balancing, compression, caching, routing.