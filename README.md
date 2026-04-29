# opbeans-swift

An opbeans-based app for the iOS Agent. This project is intended to be used with [apm-integration-testing](https://github.com/elastic/apm-integration-testing) and the other opbeans applications.

## Requirements

| Requirement | Notes |
|-------------|--------|
| **macOS** | Needed to build and run the iOS target. |
| **Xcode** | Install from the Mac App Store or [Apple Developer](https://developer.apple.com/xcode/). Use a version compatible with this project’s Swift toolchain. |
| **iOS Simulator** | At least one simulator runtime must be installed. In Xcode: **Settings → Platforms** (or **Xcode → Settings → Components** in older versions) and download an **iOS Simulator** for the OS version you want. Without a runtime, the app cannot run in Simulator. |
| **opbeans-node** | The app expects the HTTP API on port **3000** by default (see configuration below). You can run it via Docker / apm-integration-testing or from the [opbeans-node](https://github.com/elastic/opbeans-node) repo (Node.js, PostgreSQL, Redis per that project’s README). |
| **APM Server** (optional) | For traces/metrics to be sent to Elastic APM, configure `Shared/Resources/agent-conf.json`. Local stacks often use `http://localhost:8200`. |

## Running with opbeans-node

### 1. Start the backend (opbeans-node on port 3000)

**Option A — Full Elastic stack (typical for integration testing)**  

From a clone of [apm-integration-testing](https://github.com/elastic/apm-integration-testing):

```bash
./scripts/compose.py start \
  --with-opbeans-node \
  --with-opbeans-rum \
  main
```

Only the opbeans-related flags are required; the compose setup brings up the rest of the stack (APM Server, Elasticsearch, Kibana, etc.) by default.

**Option B — opbeans-node alone**  

Follow [opbeans-node](https://github.com/elastic/opbeans-node): install dependencies, set `PG*` / Redis env vars as needed, run `npm run db-setup` then `npm start`. By default the API listens on **port 3000**.

### 2. Point the iOS app at the API

Default backend URL is **`http://localhost:3000`** in `Shared/Resources/apiData.json`. Change `url` (and `auth` if required) if your opbeans-node uses another host or port.

On the **iOS Simulator**, `localhost` refers to your Mac, so `http://localhost:3000` matches opbeans-node running on the same machine.

### 3. Configure the Elastic APM agent (optional)

`Shared/opbeans-swift-App.swift` loads **`Shared/Resources/agent-conf.json`** at startup (`url` for the APM Server, optional `token`). Adjust these values for your environment (for example `http://localhost:8200` when using the stack from apm-integration-testing).

### 4. Build and run in Xcode

1. Open **`opbeans-swift.xcodeproj`** in Xcode.
2. Select the **`opbeans-swift (iOS)`** scheme.
3. Choose an **iOS Simulator** as the run destination (e.g. iPhone 16).
4. Run (**⌘R**).

Confirm the API responds (e.g. open `http://localhost:3000` in a browser) before debugging connection issues in the app.

## Related docs

- **[GETTING-STARTED.md](./GETTING-STARTED.md)** — Prerequisites and stack-oriented setup notes.
- **[scripts/README.md](./scripts/README.md)** — Command-line load generator and `xcodebuild` destinations.

### Data generation

Data generation TBD.

[//]: # (use `./script/generate-data.py` to prep and run `ios-integration-testing` with a random selection of attributes from the files in `./scripts/data` applied to the Resources object.)

[//]: # ()
[//]: # (`generate-data.py` accepts a several parameters to set app installation destination, and collector / opbeans configurations. A list of possible desinations can be provided by `xcodebuild -showdestinations`.  The default value is `"platform=iOS Simulator,name=iPad &#40;8th generation&#41;"` more details can be found calling `python3 ./script/generate-data.py -h`.)

[//]: # ()
[//]: # (Agent configuration must be done through `ios-integration-testing` for this script.)
