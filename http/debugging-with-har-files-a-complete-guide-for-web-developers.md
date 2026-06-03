# Debugging with HAR Files: A Complete Guide for Web Developers

## What is a HAR File?

A HAR file — short for **HTTP Archive** — is a JSON-formatted record of everything your browser did during a web session. Every request, every response, every redirect, every script load, every cookie — all captured, timestamped, and saved in a single file.

Think of it as a flight recorder for your browser. Just like a plane's black box records everything that happened during a flight, a HAR file records everything that happened during a browsing session — including the moments right before something went wrong.

This makes it an incredibly powerful debugging tool. Instead of having to reproduce a bug in real time while watching the Network tab, you (or someone else) can record a HAR file _while the bug is happening_, then hand it off for analysis at any time, in any environment.



## Why Not Just Use the Network Tab?

The browser's Network tab is great for live debugging, but it has critical limitations:

| Situation                             | Network Tab                        | HAR File                               |
| ------------------------------------- | ---------------------------------- | -------------------------------------- |
| Page navigates away                   | ❌ Clears all requests              | ✅ Preserves everything                 |
| Bug happens in a new tab/window       | ❌ Only covers current tab          | ✅ Can record across windows            |
| Bug is intermittent                   | ❌ You might miss it                | ✅ Records in background until it fires |
| Need to share with a colleague        | ❌ Can't share live state           | ✅ Export and send the file             |
| Need to replay and inspect later      | ❌ Gone on refresh                  | ✅ Importable and replayable            |
| Popunder or redirect opens new window | ❌ Misses the new window's requests | ✅ Captures the triggering chain        |

The Network tab is for watching things happen live. HAR files are for forensic investigation — especially when bugs are intermittent, involve redirects, or need to be handed off.



## The Structure of a HAR File

A HAR file is a JSON object. At the top level it looks like this:

```json
{
  "log": {
    "version": "1.2",
    "creator": { "name": "Chrome DevTools", "version": "120" },
    "pages": [...],
    "entries": [...]
  }
}
```

The two important arrays are `pages` and `entries`.

#### Pages

Each page represents a top-level navigation — i.e. when the browser loaded a new URL. If you visited three pages in your session, there are three page objects, each with a `startedDateTime` and a unique `id`.

```json
{
  "id": "page_1",
  "startedDateTime": "2024-01-15T10:23:44.123Z",
  "title": "https://waxlondon.com/",
  "pageTimings": {
    "onContentLoad": 1240,
    "onLoad": 3456
  }
}
```

#### Entries

This is where all the detail lives. Each entry is a single HTTP request/response pair. A typical page load might have hundreds of entries — one for the HTML, one for each CSS file, one for each JavaScript file, one for each image, one for each API call, and so on.

A single entry looks like this:

```json
{
  "startedDateTime": "2024-01-15T10:23:44.200Z",
  "time": 245,
  "request": {
    "method": "GET",
    "url": "https://a.opumo.net/oa.js",
    "headers": [...],
    "cookies": [...]
  },
  "response": {
    "status": 200,
    "statusText": "OK",
    "headers": [...],
    "content": {
      "mimeType": "application/javascript",
      "text": "..."
    }
  },
  "_initiator": {
    "type": "script",
    "stack": {
      "callFrames": [
        { "url": "https://www.googletagmanager.com/gtm.js?id=GTM-KWBP87", "lineNumber": 213 }
      ]
    }
  },
  "timings": {
    "dns": 12,
    "connect": 45,
    "send": 0.5,
    "wait": 180,
    "receive": 7
  }
}
```

The key fields to understand:

* `request.url` — what was requested
* `response.status` — what happened (200 = OK, 302 = redirect, 404 = not found, etc.)
* `response.content.text` — the actual response body (the script content, JSON payload, HTML, etc.)
* `_initiator` — **what triggered this request** — this is how you trace the chain
* `timings` — how long each phase took (DNS, connection, waiting for response, receiving data)



## The Lifecycle of a HAR Recording

Here is what happens from the moment you start recording to the moment you export:

```
1. You open DevTools → Network tab
         ↓
2. Browser starts capturing every network event
         ↓
3. You navigate to the site / reproduce the issue
         ↓
4. Every request is logged in memory:
   - URL
   - Method (GET, POST, etc.)
   - Status code
   - Request headers and body
   - Response headers and body
   - Initiator (what triggered it)
   - Timing breakdown
         ↓
5. You export the HAR file
         ↓
6. All captured entries are serialised to a JSON file
         ↓
7. The file can be imported, shared, or analysed
```

"Preserve Log" keeps step 2–4 running even across page navigations. Without it, the log resets every time the page changes.



## How to Record a HAR File

#### Chrome / Edge

1. Open DevTools (`F12` or `Cmd+Opt+I` on Mac)
2. Go to the **Network** tab
3. Tick **"Preserve log"** — this prevents the log from clearing on navigation
4. Tick **"Disable cache"** — this ensures you see fresh requests, not cached ones
5. The red circle in the top-left means recording is already active
6. Navigate to the site and reproduce the issue
7. When done: right-click anywhere in the request list → **"Save all as HAR with content"** — or click the download icon (↓) in the toolbar

#### Firefox

1. Open DevTools → **Network** tab
2. Tick **"Persist Logs"** in the toolbar
3. Browse and reproduce the issue
4. Right-click in the request list → **"Save All As HAR"**

#### Safari

1. Open Web Inspector → **Network** tab
2. Reproduce the issue
3. Right-click → **"Export HAR"**



## How to Read a HAR File

#### Option 1 — Re-import into Chrome (easiest)

1. Open Chrome DevTools → Network tab
2. Drag and drop the `.har` file into the Network panel
3. It replays as if the requests just happened — you can filter, click into requests, inspect headers and bodies

#### Option 2 — Online HAR Analysers

* **har.tech** — upload and browse visually, good for general analysis
* **toolbox.googleapps.com/apps/har\_analyzer** — Google's own tool, excellent for spotting errors and redirect chains
* **saml-tracer** (Firefox extension) — useful for auth-related debugging

#### Option 3 — VS Code or any text editor

Since HAR is JSON, you can open it directly and search for anything. Not readable at a glance, but excellent for finding specific domains, status codes, or payloads.



## How to Find a Malicious Redirect Chain in a HAR File

This is exactly how the `opumo.net` redirect was found on a compromised Shopify store. Here is the step-by-step process:

#### Step 1 — Search for 302 status codes

Every redirect hop has a `302` (or `301`) status. Open the HAR in VS Code and search:

```
"status": 302
```

Each result is a redirect. Note the `url` in the request and where it redirects to — that's in the `Location` response header. Follow the chain of redirects until you reach the final destination.

#### Step 2 — Search for unfamiliar domains

If you know your legitimate third parties (Google, Meta, your analytics platform), search for anything that isn't on that list. In the waxlondon case:

```
opumo.net
life4life.org
otieu.com
tmll7.com
routescdn.net
```

None of these belong on a clothing retailer's site. One search and they're immediately visible.

#### Step 3 — Look for polling patterns

A script that polls a server repeatedly (checking in every few seconds) is unusual. Look for multiple requests to the same URL pattern:

```
index.php?cs=
```

If you see the same endpoint hit 10, 20, 50 times in a session — that's a polling script. Check what the responses contain. Most will say `{}` (do nothing). If one contains a URL or payload, that's the moment the redirect fired.

#### Step 4 — Trace the initiator chain

Each entry has an `_initiator` field. This tells you what script triggered the request. Work backwards:

```
routescdn.net          ← final destination
  initiated by: tmll7.com
  initiated by: otieu.com
  initiated by: life4life.org
  initiated by: oa.js (opumo.net)
  initiated by: GTM container (googletagmanager.com)
  initiated by: pandectes-rules.js
  initiated by: waxlondon.com (the page itself)
```

This is the full attack chain — reconstructed entirely from the `_initiator` fields in the HAR file.

#### Step 5 — Read the response bodies

HAR files capture response content. Open the entry for `index.php?cs=<hash>` and look at `response.content.text`. You will see either `{}` (the empty "do nothing" response) or the payload containing the redirect URL. That payload is concrete evidence of the attack.



## Real Example: How the Opumo Attack Was Discovered

Here is exactly what Leaf Agency found in the HAR file for waxlondon.com:

1. A request to `a.opumo.net/oa.js` — loading a script via a GTM tag
2. Repeated polling requests to `a.opumo.net/index.php?cs=<hash>` — the script checking in with its server
3. One polling response that returned a URL instead of `{}`
4. A request to `life4life.org/tm/index.php?sid=<uuid>` — the intermediary
5. A 302 redirect to `otieu.com/4/10396626` — a PropellerAds popunder zone
6. A 302 redirect to `tmll7.com/8833957/` — a second ad zone
7. A final redirect to `routescdn.net/?cid=...&cost=0.000995&country=GB` — the monetisation endpoint, paying $0.000995 per hijacked visitor

None of this was visible in the Network tab because:

* The polling mostly returns `{}` — you'd have to watch for the exact moment the payload fires
* The redirect opened in a popunder — a new window with its own DevTools
* The attack was conditional and intermittent by design

The HAR file caught it all because it was recording in the background, capturing every request including those in the popunder window.



## HAR Files and Privacy

HAR files contain everything — including sensitive data. Before sharing a HAR file:

* **Authentication tokens** — session cookies and bearer tokens are captured. Anyone with the HAR can potentially use them to log into the site as you.
* **Passwords** — if you filled in a login form during recording, the POST body is in the HAR.
* **Personal data** — any API responses containing user data are included.

Before sharing a HAR file with a third party:

1. Open it in a text editor and search for `Authorization`, `Cookie`, `password`, `token`
2. Redact sensitive values (`"value": "[REDACTED]"`)
3. Or use Google's HAR Sanitizer tool before sending

For internal debugging within your own team, HAR files are safe to share as-is.



## Practical Tips

**Always tick "Preserve Log" before you start.** Without it, any navigation clears your entire log and you lose the trail.

**Record longer than you think you need to.** Intermittent bugs may take several minutes to fire. Leave the HAR recording running while you browse normally.

**If a redirect opens a new window or tab**, immediately open DevTools in that new window too and start recording before it redirects further. Export both HAR files.

**Compare two HAR files** — one from when the bug was happening and one from when it wasn't. The difference between them points directly at the cause.

**File size matters.** A HAR file from a heavy site can be 50–100MB. If you need to share it, zip it first or use a HAR analyser tool to filter down to relevant entries before exporting.

**HAR files are timestamped.** Every entry has a `startedDateTime`. This lets you reconstruct the exact sequence of events in chronological order, which is essential when the attack chain spans multiple domains.



## Summary

A HAR file is the most forensic-grade tool available for debugging network-level issues in a browser. It captures everything — requests, responses, bodies, timings, and initiator chains — across the entire session, including redirects and popunders that the live Network tab would miss.

For intermittent bugs, malicious redirects, third-party script issues, and anything that needs to be handed off for analysis, recording a HAR file should be your first move — not your last.

The workflow is simple:

1. Open DevTools → Network → tick Preserve Log + Disable Cache
2. Browse normally and wait for the bug to occur
3. Export the HAR
4. Search for unfamiliar domains, 302 redirects, and polling patterns
5. Trace the `_initiator` chain from the symptom back to the root cause
