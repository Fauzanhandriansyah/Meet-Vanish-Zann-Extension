# Meet Vanish Zann

A local-only Chrome extension that replaces your outgoing Google Meet camera image with a captured still of your empty room. It does not modify call audio or send data anywhere.

## Install

1. Download and unzip the extension package.
2. In Chrome, open `chrome://extensions`.
3. Turn on **Developer mode**.
4. Select **Load unpacked** and choose the unzipped `meet-vanish-extension` folder (the folder containing `manifest.json`).
5. Open a Google Meet call or the pre-join camera preview and allow camera and microphone access as usual.

Chrome 116 or newer is required for MAIN-world content scripts and the local offscreen hand detector.

1. In the Meet page's floating panel, choose **Capture empty room**.
2. Step out of frame during the visible three-second countdown. The captured JPEG stays in `chrome.storage.local` on this device.
3. Return to frame and select **Vanish / Appear**, or press **Alt+Shift+V**. Select it again to return to live video.
4. Optional: turn on snap detection and grant microphone access if Chrome asks. The sensitivity setting affects snap detection only.
5. Optional: enable **Thanos dust** for roughly 2.2 seconds of simultaneous fine-grain breakup. It starts on toggle without a mask-preparation pause. Reappearing runs the effect backwards; reversing mid-animation reuses the existing cloud. Stay in frame—you do not need to physically move away during the effect.
6. Optional: enable **V-sign detection**, wait for the local model to load, and hold your index and middle fingers apart in a V with your ring and pinky folded for about 0.7 seconds. Lower your hand for at least half a second, then show V again to reappear. A 2.3-second cooldown prevents rapid repeated triggers.

The panel can be dragged, collapsed to its small round button, and repositioned later. Use **Retake** to replace the saved room image.

## Tips

- Capture with the same lighting you expect during the call.
- Keep the camera still; moving the laptop or camera changes the room framing.
- Test snap sensitivity in the Meet preview before joining.
- For V detection, keep Meet visible and put your whole hand clearly in frame. Either hand works; point your palm generally toward the camera. It checks the raw camera even while the outgoing video shows the saved empty room. It does not request an additional camera or microphone.
- Dust keeps the captured room fixed and uses local segmentation for the person only. Remaining attached pixels stay sharp and live; detached grains move rather than simply disappear through noise. Model mistakes can still clip hair/hands or include a room edge. Capture with consistent framing/lighting: switching from the live room to the fixed saved room at the start can show an exposure/framing difference. Capture a genuinely empty room: any person already in that photograph will remain in it.
- Snaps are detected locally from microphone audio. The detector uses a high-pass filter, an adaptive noise baseline, a short transient test, and a one-second debounce. Loud claps or other sharp sounds can still resemble a finger snap; use the button or shortcut when that matters.
- The optional snap detector first tries an enabled audio track Meet already requested. If none is available, it requests a separate microphone stream with browser noise processing disabled. Turning snap detection off releases that separate stream.
