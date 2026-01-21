# simdir

**simdir** is a Bash utility for iOS developers that simplifies managing data containers on iOS Simulators. It allows you to quickly push, pull, list, and delete files in an app's sandbox without manually hunting through the obscenely long UUID paths in `~/Library/Developer/CoreSimulator/`.

## Features

* **Smart Copy (`cp`)**: Automatically detects if you are "pushing" a local file to the simulator or "pulling" a remote file from the simulator based on file existence.
* **Device Filtering**: Target specific simulators using `--booted` or Short IDs (e.g., `--device 72F0`).
* **Deep Linking**: List files in specific subdirectories (e.g., `Library/Caches`) or the default `Documents` folder.
* **Data-First Strategy**: Scans *all* installed containers to find your app, even if you don't know the full UUID.

---

## Installation

1. Save the script as `simdir` in a directory included in your `$PATH` (e.g., `/usr/local/bin` or `~/bin`).
2. Make it executable:
```bash
chmod +x ~/bin/simdir

```



---

## Usage

### 1. Listing Installed Apps

View all apps installed across your simulators. The output displays the Device Name, OS, Short UUID, and whether it is currently booted.

```bash
# List apps on ALL simulators
simdir apps

# List apps only on the currently BOOTED simulator
simdir --booted apps

# List apps on a specific device using a Short UUID
simdir --device 90AE apps

```

**Example Output:**

```text
Device: 90AEDEA7 iPhone 15 Pro (iOS-17-2) ** Booted
  • com.mydomain.mycoolapp
  • com.mydomain.helloworld

Device: 57AD570D iPad Pro 11-inch (iOS-16-4)
  • com.mydomain.taskmanager

```

### 2. Listing Files (`ls`)

By default, `ls` shows the contents of the app's `Documents` directory. You can also specify a subdirectory.

```bash
# List Documents directory
simdir com.mydomain.mycoolapp ls

# List a specific folder (e.g., Documents/Images)
simdir com.mydomain.mycoolapp ls Images

# List a folder in the App Root (e.g., Library/Caches)
simdir com.mydomain.mycoolapp ls Library/Caches

```

### 3. Smart Copy (`cp`)

The `cp` command is bidirectional. It intelligently decides whether to **Push** or **Pull** based on whether the source file exists on your Mac.

#### Pulling (Simulator -> Mac)

If the source file **does not exist** locally, `simdir` assumes it is inside the App's `Documents` folder and pulls it to your machine.

```bash
# Pull 'database.sqlite' from App Documents to current directory
simdir com.mydomain.taskmanager cp database.sqlite .

# Pull 'screenshot.jpg' from App to a local folder
simdir com.mydomain.taskmanager cp screenshot.jpg ./Screenshots/

```

#### Pushing (Mac -> Simulator)

If the source file **exists** locally, `simdir` pushes it to the App's `Documents` folder.

```bash
# Push local 'config.json' to App Documents
simdir com.mydomain.mycoolapp cp config.json .

# Push local 'avatar.png' to 'Documents/Images/' in the App
simdir com.mydomain.mycoolapp cp avatar.png Images/

```

### 4. Removing Files (`rm`)

Delete files from the app's `Documents` or `tmp` directory.

```bash
simdir com.mydomain.helloworld rm old_session.log

```

---

## Advanced Options

### Targeting Specific Devices

If you have the same app installed on multiple simulators, `simdir` will default to the first one it finds unless you specify a target.

* **`--booted`**: Restricts operations to the currently running simulator.
* **`--device <ID>`**: Restricts operations to a specific simulator using the first few characters of its UUID.

**Examples:**

```bash
# List files for 'mycoolapp' ONLY on the booted device
simdir --booted com.mydomain.mycoolapp ls

# Push a file to the specific iPad with ID starting with 57AD
simdir --device 57AD com.mydomain.taskmanager cp test_data.json .

```

---

## Troubleshooting

* **"Multiple simulators running"**: If you use `--booted` and have a Watch and iPhone simulator running simultaneously, `simdir` may ask you to be more specific. Use `--device <ID>` to resolve this.
* **"File not found"**: When pulling files, `simdir` checks `Documents` first, then `tmp`. If your file is deep inside `Library/Application Support`, you currently need to use the full path handling or stick to `ls` to find it.