# PageTML: Output
PageTML tries to automatically record user output.
We all know experiments in which some data were not collected for some reason.
PageTML is trying to prevent that from happening by automatically recording data 
in a safe way so that no data are overwritten or unrecorded.
The raw output is therefore long and maybe much of it is not needed, 
but it never hurts to collect data as long as it does not encroach on privacy rights or anonymity.
These variables should be disclosed to the participant.
This way, researchers can safely explore other participant behavior.

## Storing data as CSV or TSV file
The data are stored in a JavaScript object (essentially JSON) which includes an object array 
`outObj.slides` for information that is collected in each slide. 
When data are formatted as CSV or TSV, all fields (leafs) are turned into separate columns 
while each slide takes up one column. 
The number of rows is therefore determined by the number of slides a participant as already seen 
(including backward navigations, e.g., with `<p-back>`). When data ought to be stored as a CSV or TSV file, 
ensure that the `<p-download>` or `<p-upload>` file contains the attribute
`format="csv"` or `format="tsv"`.

## Fields in the output object

- `userAgent`: Information of browser runtime and stack information, CSS engine and browser engine.
  **example** for information saved: `"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/153.0.0.0 Safari/537.36 Edg/153.0.0.0"`
  1. `"Mozilla/5.0"` - Most modern desktop browsers send this (or `Netscape`) which is considered a historical compatibility prefix. It does not mean Firefox or Netscape.
  2. `"(Windows NT 10.0; Win64; x64)"` - information about the operating system and central processing unit. `Windows NT 10.0` is both Windows 10 and Windows 11. `Win64; x64` means 64-bit Intel/AMD.
  3. `"AppleWebKit/537.36"` - This segment is important for CSS rendering and indicates the CSS engine. This may sometimes also indicate Chromium instead of WebKit. Please note that the version token `537.36` has not tracked a real WebKit release for years.
  4. `"(KHTML, like Gecko)"` - This can be ignored as it was rather added for compatibility: KHTML is the old Konqueror engine and was added (like Gecko) so that websites that sniffed for Firefox would still work.
  5. `"Chrome/153.0.0.0"` - This indicates the browser engine (especially for JavaScript) is Chromium version `153`. The .0.0.0 is intentional: under user-agent reduction, some browsers like Chrome and Edge fix the builds/patches fields to 0 even if it is technically incorrect.
  7. `"Safari/537.36"` - Again, this fragment can be ignored as it is only included for browser compatibility reasons. It again includes the fixed AppleWebKit version. This is not Safari!
  8. `"Edg/153.0.0.0"` - At last, this is the real product marker. `Edg` (not Edge) means Edge running on the Chromium engine. Legacy EdgeHTML used `Edge/`. Same major version as the `Chrome/` token.
- `url`: The full URL to the experiment that the participant clicked.
- `maxScreenHeight`: Maximum height of the (first-used) screen. This variable is determined at the beginning of the experiment.
- `maxScreenWidth`: Maximum width of screen as determined at the beginning of the experiment.
- `colorDepth`: Color depth is the color depth of the current application or client. In modern browsers, it is virtually identical to `pixelDepth`.
  It may be important to check this requirement to determine whether the screen's color depth is sufficient for an experiment.
- `pixelDepth`: Pixel depth indicates the number of bits allocated to colors for a pixel in the monitor (or output screen), excluding the alpha channel. It may return 24 in some browsers for compatibility reasons. The information is extracted from `window.screen.pixelDepth`.
- `language`: browser language extracted from `window.navigator.language` at the beginning of the experiment.
- `slide`: slide number (including slides from backwards navigations with, e.g., `<p-back>`
- `fullscreen`: Indicates whether a screen was in fullscreen mode. 
- `htmlBranch`: representing the ancestors within the HTML document's body. 
  This way, one knows where the current slide is located within the larger context of the survey or experiment.
  When an `id` or `name` is provided, further attributes are not necessary to identify the slide's location while keeping the HTML branch brief. Here is an **example**: 
  `"body>p-while[name=practiceLoop]>p-while[name=practiceStimuli]>p-slide[name=fixcross]"`
  Reading from right (leaf) to left (the stem), the slide seems to display a fixation cross in some experiment. 
  It is part of a set of practice stimuli which belong to a practice loop (e.g., to ensure participants reach some level of accuracy). 
  Finally, the practice loop's parent element is the document's body.
- `screen_height`: Height of the window when the experiment is loaded, as determined from `window.screen.availHeight` at the beginning of the experiment.
- `screen_width`: Width of the window when the experiment is loaded, as determined from `window.screen.availWidth` at the beginning of the experiment.
- `screen_orientation`: orientation type of the screen determined when the experiment (from the URL) is loaded.
  The options are `"portrait-primary"`, `"portrait-secondary"`, `"landscape-primary"`, or `"landscape-secondary"`.
- `durationTimeMS`: total duration of a slide being presented. For performance reasons, 
  some slides might lag a bit in terms of their presentation duration. 
  Hence, the total duration lets researchers assess the true duration time 
  that a slide was viewed based on graphical elements and computations.
- `absoluteTimeMS`: absolute time integer stored at the beginning of each slide.
- `meta`: general attributes
  - `meta.subj`: subject (participant) code
  - `meta.session`: session code for a participant, especially important when a participant re-opens the experiment in multiple sessions (as in longitudinal studies).
- `attributes`: The current slide's attributes all have the prefix "attributes.":
  - `attributes.name`: A slide's name.
  - `attributes.id`: A slide's ID.
  - `attributes.keysnext`: indicates which keys on the keyboard  
  - `attributes.maxms`: A slide's maximum presentation time until the browser automatically moves on to the next slide.
    That is unless the participant presses a button written in `keysnext`
  - ...
- `pointer`: collected information from mouse clicks or touch events.
  - `pointer.t`: Time of the pointer event in milliseconds since the slide became visible to the participant. If the participant continues to a slide and then clicks on something after 1.2s, `pointer.t` will be 1200.
  - `pointer.x`: x coordinate on the screen where the mouse or touch event; the screen's top side is 0.
  - `pointer.y`: y coordinate on the screen where the mouse or touch event; the screen's left side 0.
  - `pointer.f`: force applied to the touch event (`null` if mouse event or if force detection is not supported by the device).
  - `pointer.rx`: width radius of a touch event (`null` if mouse event).
  - `pointer.ry`: height radius of a touch event (`null` if mouse event).
  - `pointer.ang`: angle of the touch event (rotation of the touch ellipse).
  - `pointer.el0`: element on which the mouse is clicked down or the finger starts touching the screen. An entry is either an element with an id (then the entry starts with `#`) or the tag name is followed by the text content like `label:"right-handed"`)
  - `pointer.el1`: element on which the mouse is released or when the finger gets away from the screen.
  - `pointer.type`: type of the pointer event which is either `"touch"` or `"mouse"`
- `dragdrop`: 
  - `dragdrop.t`: 
  - `dragdrop.dragged`: 
  - `dragdrop.container0`: Container which the element was picked up from.
  - `dragdrop.container1`: Container which the element was dropped into.
  - `dragdrop.order1`: Order of elements in the container in which the element was dropped.
- `key`: all keyboard events, including key-down and key-up events.
  - `key.down`: The key that was pressed down.
    - `key.down.t`: Time at which a key was pressed down.
    - `key.down.k`: The key that was pressed down.
  - `key.up`: The key that was pressed down.
    - `key.up.t`: Time at which a key was released.
    - `key.up.k`: The key that was released.
- `content`: input from any element with an `id` or a `name` attribute. 
  All entries here are defined by the experimenter.
  - ...
- `variables`: storing JavaScript variables within `p-var` when the slide is loaded.
  - ...

## Outlook
Over time, the output and collected data will be expanded based on experimenters' needs.






