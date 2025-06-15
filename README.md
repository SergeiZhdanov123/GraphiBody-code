
> **Note:** You only need the `BodyGraph.aia` file. All source, assets, and logic are contained within it.

---

## 🚀 Getting Started

### Prerequisites

- **MIT App Inventor 2** – A free, web‐based tool for building Android apps.  
  Website: https://appinventor.mit.edu/

- **Google Account** – Required to sign in and use MIT App Inventor.

### Importing the Project

1. Go to the [MIT App Inventor](https://ai2.appinventor.mit.edu/) website and sign in with your Google account.
2. In the top‐right corner, click **“Projects” → “Import project (.aia) from my computer”**.
3. Select `BodyGraph.aia` from your local machine.
4. The project will load into the Designer and Blocks editors.

---

## 🎯 How It Works

1. **Camera & Pose Detection**  
   - When you tap the **“Start”** button, the app activates your device camera and begins real-time body‐pose detection (via the built‐in Pose component).

2. **Graph Generation**  
   - The app generates a random line graph pattern (e.g., a series of 5–7 points connected by straight lines).

3. **Alignment Check**  
   - As you move to align your body pose to each graph segment, the app analyzes your joint positions and gives feedback:
     - **✓** When your detected pose matches the current segment.
     - **→** Prompt to adjust up/down/left/right.
   - Proceed through all segments; final score indicates how closely you matched the pattern.

4. **Results Screen**  
   - Shows your matching score, time taken, and an option to retry with a new graph.

---

## 🛠️ Customization

- **Graph Complexity**: In the Blocks editor, adjust the number of graph points or allowed tolerance for pose matching.
- **UI Colors**: In the Designer view, modify the theme colors (default: #FFD500 for accents, #000000 background).
- **Feedback Messages**: Edit text labels to change instructions, tips, or scoring messages.

---

## 📱 Testing & Packaging

1. Connect your Android device via USB (or use the AI2 Companion app).
2. Click **“Connect” → “AI Companion”** in MIT App Inventor.
3. Scan the QR code (or enter the 6‐digit code) to live‐test the app on your device.
4. To generate an APK for distribution:  
   - Click **“Build” → “App (provide apk)”**, then download the compiled APK.

