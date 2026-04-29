# ioBroker.victronvrm - Developer Context

This project is an **ioBroker adapter** for the **Victron Energy VRM Portal**. It fetches real-time diagnostic data and energy statistics for Victron power systems (Solar, Battery, Inverters) using the official VRM REST API v2.

## 🚀 Project Overview

- **Main Functionality:** Polls the VRM API for diagnostics (current values) and overall stats (historical energy flows).
- **Core Technology:** Node.js, [ioBroker Adapter Core](https://github.com/ioBroker/adapter-core).
- **Key API Endpoints:**
  - `/diagnostics`: Current state of all connected devices (Battery, MPPT, MultiPlus, etc.).
  - `/overallstats`: Cumulative energy values (Today, Week, Month, Year).
  - `/users/me`: Metadata (Timezone, Site Name).

## 🛠️ Building and Running

### Development Commands
- **Testing:** `npm run test` (Runs unit, integration, and package files tests).
- **Linting:** `npm run lint` (ESLint v9 with Flat Config).
- **Translation:** `npm run translate` (Updates `admin/i18n` using `translate-adapter`).
- **Releasing:** `npx release-script [patch|minor|major]` (Automated versioning and changelog).

### Local Execution
To run the adapter in debug mode:
1. Ensure `ioBroker` is installed locally or use the `@iobroker/testing` environment.
2. Run `node main.js --debug` (requires `iobroker-data` setup).

## 🏗️ Architecture & Conventions

### File Structure
- `main.js`: Entry point. Manages the adapter lifecycle (`onReady`, `onUnload`), intervals, and state updates.
- `lib/vrm-api.js`: Encapsulated API client. Handles authentication, rate limiting (429 retries), and fetching.
- `lib/sensor-definitions.js`: The "brain" of the mapping. Maps VRM `idDataAttribute` to ioBroker State IDs, roles, and units.
- `admin/`: Configuration UI (uses `jsonConfig.json` for modern UI).

### Coding Standards
- **ioBroker Best Practices:**
  - Use `this.setObjectNotExistsAsync` to avoid overwriting user-customized objects.
  - State values from API must have `ack: true`.
  - Commands/user inputs must have `ack: false`.
  - Use `this.setTimeout` / `this.setInterval` (not globals) for automatic cleanup on unload.
- **Logging:** All logs must be in **English**.
- **Roles:** Use specific ioBroker roles (e.g., `value.battery`, `value.power`) instead of generic `state`.
- **Async/Await:** All I/O and adapter calls are asynchronous.

### Dependency Management
- **Adapter Core:** Always use `@iobroker/adapter-core`.
- **Testing:** Mocha/Chai/Sinon are used for unit and integration testing.

## 📝 Configuration (Native)
Defined in `io-package.json`:
- `accessToken`: Personal Access Token (encrypted/protected).
- `idSite`: VRM Installation ID.
- `pollInterval`: Seconds between diagnostic updates (default 60s).
- `statsInterval`: Seconds between energy stat updates (default 300s).

## 🧩 Key Constants (`sensor-definitions.js`)
- `ALL_SENSORS`: Map of attribute IDs to object schemas.
- `CHANNELS`: Hierarchy definitions (battery, solar, grid, etc.).
- `TEXT_STATES`: Mapping for states like VE.Bus mode or Solar Charger state.
