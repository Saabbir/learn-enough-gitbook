---
description: >-
  Understanding system information, CPU details, memory usage, platform
  detection, uptime, and how Node.js interacts with the host machine
---

# 📘 CHAPTER 5 — OS Module

## 1. Introduction

Every Node.js application runs on a computer.\
That computer could be:

a Windows machine\
a Linux server\
a macOS laptop\
a Docker container\
a cloud VM\
a Raspberry Pi

But how does your Node.js app know:

what operating system it’s running on?\
how many CPUs are available?\
how much memory the machine has?\
where the system’s temp directory is?\
what the hostname is?\
which platform it’s running on?

This is where the **os** module comes in.

The `os` module provides information about the machine on which your Node.js app is running.\
It is essential for system-level scripts, servers, dev tools, CLIs, monitoring tools, and apps that need to behave differently based on OS or hardware.

Let’s explore it step-by-step.

## 2. What the OS Module Does

The os module lets you:

check CPU details\
check total or free memory\
get platform type\
find home directory\
locate temporary directory\
get network interfaces\
discover uptime\
inspect load averages\
detect architecture (x64, arm64)\
identify line endings (Windows vs Unix)

Load it like any other built-in module:

```js
const os = require("os");
```

## 3. os.hostname()

Returns the machine’s hostname:

```js
os.hostname();
```

Example:

```
"Saabbir-MacBook"
```

Useful for logging, distributed systems, identifying servers, or prefixing filenames.

## 4. os.platform() and os.type()

platform returns simplified OS type:

```js
os.platform();
```

Possible values:\
`win32`, `darwin`, `linux`, `aix`, `freebsd`, `openbsd`

type returns the full OS name:

```js
os.type();
// "Windows_NT", "Darwin", "Linux"
```

Use this to write OS-specific behavior.

## 5. os.arch()

Tells you the CPU architecture:

```js
os.arch();
```

Examples:\
`x64`, `arm64`, `arm`, `ia32`

Useful when:

building binaries\
using native modules\
targeting Raspberry Pi (arm64)\
packaging applications

## 6. os.cpus()

Returns details about each CPU core:

```js
os.cpus();
```

Example structure:

```
[
  {
    model: 'Intel(R) Core(TM) i7…',
    speed: 2600,
    times: { user: 10300, nice: 0, sys: 5200, idle: 90000, irq: 0 }
  },
  ...
]
```

Useful for:

optimizing worker threads\
scaling with cluster\
monitoring system performance

To know how many CPU cores exist:

```js
os.cpus().length;
```

You’ll use this when spawning clusters:

```js
const numCPUs = os.cpus().length;
```

## 7. os.totalmem() and os.freemem()

Returns memory in **bytes**:

```js
os.totalmem();
os.freemem();
```

Convert bytes to MB or GB:

```js
const gb = (os.totalmem() / 1024 / 1024 / 1024).toFixed(2);
```

These values help with:

monitoring\
resource allocation\
preventing out-of-memory crashes\
logging system performance

Example log message:

```js
console.log(`Free memory: ${os.freemem() / 1024 / 1024} MB`);
```

## 8. os.uptime()

Returns system uptime in seconds:

```js
os.uptime();
// e.g., 84291
```

You can convert to hours or days:

```js
(os.uptime() / 3600).toFixed(1) // hours
```

Monitoring dashboards use this extensively.

## 9. os.homedir()

Returns the current user’s home directory:

```js
os.homedir();
```

Example:

`/Users/saabbir`\
or\
`C:\Users\Saabbir`

Use this when storing:

config files\
logs\
temp scripts\
cache files

## 10. os.tmpdir()

Returns the system temporary folder:

```js
os.tmpdir();
```

Examples:

macOS → `/var/folders/.../T/`\
Linux → `/tmp`\
Windows → `C:\Users\Saabbir\AppData\Local\Temp`

This is where Node apps store:

temporary uploads\
temporary logs\
build artifacts\
cache data

Your OS cleans this location automatically.

## 11. os.endianness()

Returns byte order of the CPU: `LE` or `BE`

```js
os.endianness();
```

Most modern CPUs are `LE` (little-endian).\
Useful when working with binary protocols or Buffers.

## 12. os.networkInterfaces()

This gives detailed network information:

```js
os.networkInterfaces();
```

Example output:

```
{
  en0: [
    { 
      address: '192.168.1.100',
      netmask: '255.255.255.0',
      family: 'IPv4',
      mac: '82:4f:ab:fe:f1:2a',
      internal: false
    }
  ]
}
```

Useful for:

determining external IP\
running servers on the correct interface\
building networking tools\
monitoring dashboards

## 13. os.release()

Operating system version:

```js
os.release();
// e.g. "22.3.0" for macOS Ventura or "10.0.19045" for Windows 10
```

Good for debugging.

## 14. os.loadavg() (Linux/macOS Only)

```js
os.loadavg();
```

Returns CPU load averages for the last 1, 5, and 15 minutes.

Example:

```
[0.52, 0.34, 0.29]
```

This is the same number shown by the `top` command.

Not supported on Windows (always returns \[0, 0, 0]).

## 15. os.version()

Returns the OS version string:

```js
os.version();
// "Darwin Kernel Version 22.5.0"
```

Useful for debugging and environment detection.

## 16. Real-World Use Cases

Detecting production vs local environment:

```js
if (os.platform() === "win32") {
  console.log("Windows detected");
}
```

Using CPU count for clusters:

```js
const workers = os.cpus().length;
```

Monitoring free memory before processing a large file:

```js
if (os.freemem() < 500_000_000) {
  console.log("Not enough memory to continue");
}
```

Creating temporary files:

```js
const filePath = path.join(os.tmpdir(), "upload-" + Date.now());
```

Detecting system hostname in logs:

```js
console.log("Running on:", os.hostname());
```

Choosing the correct network interface (IPv4):

```js
const nets = os.networkInterfaces();
const ip = nets.eth0?.find(v => v.family === "IPv4")?.address;
```

## 17. Common Mistakes Beginners Make

Confusing total RAM with free RAM\
Forgetting memory values are in bytes\
Using os.loadavg() on Windows\
Assuming directory paths are the same on all OS\
Not using os.tmpdir() for temporary files\
Hardcoding paths instead of using homedir or tmpdir

## 18. Summary

You now understand everything important about the os module:

hostname\
platform and OS type\
architecture\
CPU details\
memory usage\
uptime\
home directory\
temporary files directory\
network interfaces\
OS version information\
endianness\
load average

The os module is small but incredibly useful for debugging, logging, scaling servers, and making your applications environment-aware.
