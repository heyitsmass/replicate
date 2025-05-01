## Replicate - Website Snapshot & Local Development Environment

- [Replicate - Website Snapshot \& Local Development Environment](#replicate---website-snapshot--local-development-environment)
  - [Goal/Vision](#goalvision)
  - [Core Features](#core-features)
  - [Key Components / Architecture](#key-components--architecture)
  - [Tech Stack](#tech-stack)
  - [Potential Challenges](#potential-challenges)

### Goal/Vision

To create a tool that fetches a live website's front-end assets (HTML, CSS, JS), sets up a local server to serve them, and enables developers to modify these assets locally with hot-reloading for rapid prototyping and testing directly against production APIs (assuming CORS allows).

_This is just for fun, You can probably just use developMode in the browser_

### Core Features

-   **Website Snapshotting:** Fetch the HTML source of a given URL and recursively download linked CSS, JavaScript, and potentially image assets.
-   **Asset Rewriting:** Modify links/paths within downloaded assets (HTML `href`/`src`, CSS `url()`) to point to the locally served copies.
-   **Local Development Server:** Spin up a local HTTP server to serve the captured website files.
-   **File Watching & Hot-Reloading:** Monitor local copies of assets for changes and automatically refresh the browser or inject changes (HMR for CSS, potentially page reload for HTML/JS).
-   **Modification Workspace:** Provide a clear directory structure for the captured site where developers can edit files.

### Key Components / Architecture

-   **Fetcher/Downloader:**
    -   Takes a target URL.
    -   Downloads the root HTML document.
    -   Parses HTML to find links to CSS, JS, images, etc. (respecting `robots.txt`?).
    -   Recursively downloads discovered assets within the same origin (or configurable scope).
    -   Handles potential download errors.
-   **Asset Parser/Rewriter:**
    -   Parses HTML and CSS.
    -   Identifies relative and absolute paths for assets.
    -   Rewrites paths to be relative to the local serving structure. Handles base URLs if necessary.
-   **Local Server:**
    -   A simple static file server (e.g., `http.server`).
    -   Serves the rewritten files from the local workspace.
-   **File Watcher:**
    -   Monitors the workspace directory for file changes (e.g., using `watchdog`).
-   **Hot-Reloading Mechanism:**
    -   Injects a small WebSocket client script into the served HTML.
    -   WebSocket server (part of the local server) listens for file change events from the watcher.
    -   On change, notifies the client script to either reload the page or, for CSS, potentially request the new CSS and swap it in place.
-   **CLI / User Interface:**
    -   Command-line interface to initiate snapshot (`replicate <url>`) and start the dev server (`replicate serve`).

### Tech Stack

-   Core Logic: Python
-   HTTP Client: `requests`.
-   HTML/CSS Parsing: `BeautifulSoup`.
-   Local Server: `Flask`/`FastAPI`
-   File Watching: `watchdog`.
-   WebSockets: `websockets`.

### Potential Challenges

-   Handling complex websites with dynamically loaded content/assets via JavaScript.
-   Accurately parsing and rewriting asset paths in various contexts (HTML, CSS, JS strings).
-   Dealing with authentication/cookies if the target site requires login for full access.
-   Robustly handling various edge cases in HTML/CSS/JS syntax.
-   Potential legal/ethical implications of replicating copyrighted sites.
