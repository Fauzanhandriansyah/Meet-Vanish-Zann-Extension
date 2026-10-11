# Meet Vanish Zann

A local-only Chrome extension that replaces your outgoing Google Meet camera image with a captured still of your empty room. It does not modify call audio or send data anywhere.

## Install

1. Download and unzip the extension package.
2. In Chrome, open `chrome://extensions`.
3. Turn on **Developer mode**.
4. Select **Load unpacked** and choose the unzipped `meet-vanish-extension` folder (the folder containing `manifest.json`).
5. Open a Google Meet call or the pre-join camera preview and allow camera and microphone access as usual.

Chrome 116 or newer is required for MAIN-world content scripts and the local offscreen hand detector.

**Already had Meet open when you installed? Reload the Meet tab.** The toolbar popup can work before the page's camera wrapper and floating panel have been injected. Reloading an active call may briefly reconnect it.

### Updating an existing installation

Version 1.1.0 includes a new silhouette dust effect and optional V-sign detection. Version 1.0.1 fixed panel creation under Trusted Types enforcement. Unzip the new package, replace **all files and folders** in the folder you loaded into Chrome (including `vendor` and `models`), click the extension's reload button on `chrome://extensions`, and reload the Meet tab.

Version 1.1.1 fixes the premature blurred/ghosted dust appearance: the first frame matches the full-resolution camera snapshot, untouched areas stay fully opaque, and grains break away with a crisp removal stencil rather than a soft confidence-weighted fade. V detection, snapping, and camera behavior are unchanged.

Version 1.2.0 uses bundled, on-device person segmentation for Thanos dust. Only person pixels can produce grains; the saved room remains a separate untouched layer. Reappearance runs the same grain sequence in reverse. Replace the complete package, including the new `models/selfie_segmenter.tflite`.

Version 1.3.0 replaces square flecks with smaller anti-aliased circles, continuous size variation, gradual particle birth/fade, and gentler wind. The person layer now draws the live raw camera every render, not a frozen snapshot. During an active effect, the local silhouette is refreshed about every 180 ms with at most one pending request per pipeline. Detached grains keep their launch positions/colors, while returning particles follow the moving person.

Circular sprites are cached with transparent padding to avoid color bleeding. Full-resolution compositing is restricted to the current person's bounds instead of repeatedly processing the entire camera frame.

Version 1.4.0 starts the sweep synchronously on toggle, without awaiting a new mask. While Thanos is enabled, a small local silhouette cache is kept warm. The front starts at the person's side, moves in one ordered direction, and reveals the saved room behind it. The unswept part is the original fully opaque live camera image—not a low-resolution whole-person cutout. Segmentation now selects grain origins only, so a late/missing mask cannot blur or punch holes in the still-visible body. The front is fixed for the transition and does not jump when segmentation updates.

## Use

**Version 1.7.2 makes the person-only dust smoother without a line swap.** Each removed person cell releases its larger source-colored grain plus two finer fragments; a locally softened birth field rounds out blocky vacancies. Grain births loosely favor the right side for the reference's partly intact silhouette, but remain scattered on both sides: there is no hard cut line or whole-room swap. Grains ease into their motion and fade individually, while untouched person pixels remain opaque and the saved room stays fixed. The outgoing track remains 30 fps; during dust only, the GPU pass is capped at 1024×576 and upscaled to the original stream dimensions to bound work on high-resolution cameras. Normal camera and fully vanished frames stay at their original resolution. Reversing starts from the last frame actually displayed. This is a rendering change, not a promise of a particular frame rate on every device.

**Version 1.7.1 keeps the approved grain transport and fixes the upside-down saved room.** Captured `ImageBitmap` backgrounds are normalized to a DOM canvas before their GPU upload, so the room, live video, silhouette, and final room image all have the same top/bottom orientation. Grain cell spacing and diameter are doubled versus 1.7.0 (5–7.5 pixels at the renderer's native resolution, before display scaling), using one quarter as many grains. Birth timing, wind, source colors, and reverse paths are unchanged.

**Version 1.7.0 replaces the incorrect whole-image dissolve in 1.6.0 with GPU grain transport.** The captured room is a fixed background during the effect: it is never dotted, faded, or clipped by a moving boundary. The live detected person is fully opaque until individual grains detach. Each detached grain carries that source pixel's color and travels upward/right, leaving the saved room visible in its original location. There is no extra decorative cloud over a fading person. The same birth map controls removal and flight. Reappearance reverses those same paths.

The detector threshold is more inclusive, with one model-pixel of silhouette expansion to reduce missed hair/body edges. Remaining attached person pixels are LIVE, not a frozen portrait; detached grain colors come from a small-duration launch snapshot. If the first valid mask has not arrived, the live camera remains visible instead of graining the whole room. GPU support is required; an unavailable renderer is reported and uses an instant switch, not a disguised fade.

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
