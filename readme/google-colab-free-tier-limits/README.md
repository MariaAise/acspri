# 📚 Google Colab Free Tier limits

### GPU limits (Free tier)

**Availability**

* GPU access is **not guaranteed**
* GPUs may be unavailable at busy times
* Type varies (often T4; sometimes none)

**Session limits**

* Maximum continuous session: **\~12 hours** (often less in practice)
* Inactivity timeout: **\~30–90 minutes**
* Sessions can be **terminated early without warning**

**Usage limits**

* GPU access is **rate-limited over time**
* Heavy or repeated GPU usage may temporarily disable GPU access for your account
* After hitting limits, Colab may:
  * Fall back to CPU
  * Refuse GPU allocation for hours or days

**Implication**

* Free Colab GPUs are suitable for **short demos**, not long-running jobs
* Do not rely on GPU availability during teaching without a fallback

***

### CPU & memory limits

**RAM**

* Typically **\~12–16 GB RAM**
* Memory resets when runtime restarts

**CPU**

* Shared virtual CPUs
* Performance can vary depending on load

**Common failure mode**

* “Runtime disconnected” due to memory pressure
* Kernel crash when loading large models or datasets

***

### Runtime & execution limits

**Runtime lifetime**

* Runtime resets clear:
  * Variables
  * Installed libraries
  * Uploaded/downloaded files

**Execution**

* Long-running cells may be interrupted
* Background execution is not supported

**Best practice**

* Design notebooks so they can be re-run top to bottom without manual fixes

***

### Storage limits

**Local storage (`/content`)**

* Temporary only
* Deleted on runtime reset
* Size is limited (not designed for large datasets)

**Persistence**

* No automatic persistence unless:
  * Files are saved to Google Drive
  * Results are downloaded manually

***

### Internet & external access

**Outbound access**

* Allowed (APIs, GitHub, Hugging Face)
* Rate limits depend on the external service

**Blocked use cases**

* Running servers
* Long-lived background processes
* Some ports are restricted

***

### Library & environment limits

**Installed libraries**

* Installed per session via `pip`
* Must be reinstalled after restart

**Version control**

* You do not control the base Python image
* Occasionally, Colab updates can break older notebooks

***

```
// # =========================
# Colab: Resource + Space Checks
# Memory (RAM), disk space, GPU memory, and reset patterns
# =========================

import os, gc, sys, subprocess, textwrap
from pathlib import Path

```

***

### Code Snippets

**RAM usage (current process + system)**

```

# --- Option A: quick system view ---
!free -h

# --- Option B: current Python process memory (RSS) ---
try:
    import psutil
except ImportError:
    !pip -q install psutil
    import psutil

process = psutil.Process(os.getpid())
rss_gb = process.memory_info().rss / (1024**3)
print(f"Python process RSS: {rss_gb:.2f} GB")
```

**Disk space (what’s left in /content)**

```
// # Disk usage summary
!df -h

# Folder sizes (helps identify what's filling disk)
!du -h --max-depth=1 /content | sort -hr | head -n 20

# List large files in /content (top 20)
!find /content -type f -printf "%s\t%p\n" 2>/dev/null | sort -nr | head -n 20
```

**GPU availability + GPU memory**

```
// # Shows GPU type, total memory, used/free memory, and top GPU processes
!nvidia-smi
```

**Python-level cleanup (free RAM, clear GPU cache)**

**A) Delete big objects + run garbage collection**

```
// # Example: delete big variables (edit names as needed)
# del df, model, embeddings

gc.collect()
print("Garbage collection complete.")
```

**Soft reset: clear variables (keeps installed libs, runtime stays)**

```
// # Clears user-defined variables (does not uninstall packages)
# Use this if notebook state is messy but you don't want a full restart.
%reset -f
```
