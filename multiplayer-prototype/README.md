# Welcome to Colyseus!

This project was created with [⚔️ `create-colyseus-app`](https://github.com/colyseus/create-colyseus-app/).

[Documentation](https://docs.colyseus.io/)

## :crossed_swords: Usage

```
npm start
```

Then open http://localhost:2567 for the playground, or /monitor for the monitor.

## Structure

- `src/index.js`: entry point — leave it alone if you plan to deploy to Colyseus Cloud
- `src/app.config.js`: server configuration — rooms, HTTP routes, express middleware
- `src/rooms/MyRoom.js`: your room handler
- `src/rooms/schema/MyRoomState.js`: the state synchronized to every client in the room
- `test/MyRoom.test.js`: boots the real server and connects a real client
- `loadtest/example.js`: scriptable client for `npm run loadtest`
- `ecosystem.config.cjs`: pm2 configuration, used when deploying to Colyseus Cloud

## Scripts

- `npm start`: run the server in watch mode (`node --watch src/index.js`)
- `npm test`: run the mocha test suite
- `npm run build`: no build step — the sources run as-is
- `npm run loadtest`: connect N simulated clients with [`@colyseus/loadtest`](https://github.com/colyseus/colyseus-loadtest/)

## What's included

### Server tick

`MyRoom` runs a game loop via `setTimestep()`, which fires on a wall-clock
interval and hands you the *measured* delta in milliseconds. Clients send
movement intent (`-1..1` per axis); the server integrates it and owns every
position.

`setTimestep` is the simple loop. If you later add client-side prediction, switch
to `setFixedTimestep()` — it advances a constant `dt` through an accumulator, and
only a constant `dt` replays deterministically on the client.

- https://docs.colyseus.io/room#game-loop

### Authentication

`@colyseus/auth` mounts its routes under `auth.prefix`, giving you anonymous
sign-in and email/password registration without writing endpoints. The room's
static `onAuth` verifies the issued JWT automatically.

`src/config/auth.js` holds the storage callbacks. It ships with an **in-memory
array** so the flow works on the first run — replace `onFindUserByEmail`,
`onRegisterWithEmailAndPassword` and `onRegisterAnonymously` with real queries
before you deploy anything.

`JWT_SECRET` and `SESSION_SECRET` were generated into `.env.development` and
`.env.production`. Rotating `JWT_SECRET` logs everyone out; keep both out of
version control.

- https://docs.colyseus.io/auth/module
