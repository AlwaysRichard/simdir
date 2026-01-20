# simdir 

**simdir** is a lightweight Bash utility designed to eliminate the frustration of navigating the iOS Simulator's file system. 

Instead of hunting through obfuscated UUID paths like `~/Library/Developer/CoreSimulator/Devices/C5A5D0DD.../data/Containers/Data/Application/DFF7967A...` You can interact with your app's sandbox directly using its name.

Apps are keyed off the bundle identifier in the Apps Settings Target Genera 

---

## 🚀 Installation

1. **Create the script file**:

```bash
touch ~/simdir
chmod +x ~/simdir

```

2. **Add the logic**: Paste the Bash script code into `~/simdir`.
3. **Set up an Alias** (Recommended):
To run `simdir` from anywhere, add this to your shell profile (usually `~/.zshrc` or `~/.bash_profile`):

```bash
echo "alias simdir='~/simdir'" >> ~/.zshrc
source ~/.zshrc

```



---

## 🛠 Usage & Examples

The script automatically detects the currently booted simulator. If more than one (or zero) simulators are running, it will safely abort to prevent targeting the wrong device.

### List Installed Apps

Find out which apps are on the booted simulator and see their Bundle IDs and local paths.

```bash
simdir apps

```

### List App Files

List the contents of the app's `Documents` folder.

```bash
simdir com.mydomain.mycoolapp ls

```

### Copy File INTO Simulator

Copy a local file (like a database or a zip) into the app's `Documents` directory.

```bash
simdir GeoLog cp ~/Desktop/GeoLog-26x6ab8.zip

```

### Fetch File FROM Simulator

Pull a file out of the simulator and into your current directory. It automatically searches `Documents` and `tmp`.

```bash
simdir GeoLog get GeoLog-26x6ab8.zip

```

### Remove File from Simulator

Delete a specific file or directory from the `Documents` or `tmp` folder.

```bash
simdir GeoLog rm GeoLog-26x6ab8.zip

```

---
## 💡 Identifying Your App

While the script attempts to match by App Name, it is most reliable when using the Bundle Identifier. For example, if your app is "MyCoolApp," you may need to use: simdir com.mydomain.mycoolapp ls

### How to find your Bundle Identifier in Xcode:

If you aren't sure what your identifier is, you can find it inside your Xcode project:

1. Open your project in Xcode.
2. Select your Project in the Project Navigator (the blue icon at the top of the left sidebar).
3. Select your App Target in the main window.
4. Go to the General tab.
5. Look under the Identity section for the Bundle Identifier field.


---

## 🔍 How it Works

iOS separates an app into two distinct containers with different UUIDs:

1. **Bundle Container**: The read-only `.app` package.
2. **Data Container**: The writable sandbox containing `Documents`, `Library`, and `tmp`.

**simdir** performs a **"Data-First" scan**. It traverses the active simulator's data directory and reads the hidden `.com.apple.mobile_container_manager.metadata.plist` file inside every folder. It matches your provided app name against the `MCMMetadataIdentifier` to find the correct path, even if the folder name is a random UUID.

---

## ⚠️ Requirements

* **macOS**
* **Xcode & Command Line Tools** (`xcrun`)
* **Active Simulator**: At least one simulator must be in the "Booted" state.

## 📝 License

MIT - Feel free to use and modify for your own development workflow.


