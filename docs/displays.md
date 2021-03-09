# Displays
You can create a display like so:
```JavaScript
const displayId = "myDisplay";
const displayWidth = 960;
const displayHeight = 540;
const resized = () => {
    const { width, height } = previewWindow.getContentBounds();
    osn.NodeObs.OBS_content_resizeDisplay(displayId, width, height + 20);
    osn.NodeObs.OBS_content_setPaddingSize(displayId, 5);
};
previewWindow = new BrowserWindow({
    width: displayWidth,
    height: displayHeight,
    // if you use this, the window will automatically close
    // when the parent window is closed
    parent: parentWindow,
    useContentSize: true,
});
previewWindow.on("close", () => {
    osn.NodeObs.OBS_content_destroyDisplay(displayId);
    previewWindow = undefined;
});
previewWindow.on("resize", resized);

osn.NodeObs.OBS_content_createSourcePreviewDisplay(
    previewWindow.getNativeWindowHandle(),
    "", // or use camera source Id here
    displayId,
);
osn.NodeObs.OBS_content_setShouldDrawUI(displayId, false);
osn.NodeObs.OBS_content_setPaddingColor(displayId, 255, 255, 255);
resized();
```

You need a `BrowserWindow` and OBS will draw the display on top of it. Be sure to listen to all events and resize or move the distplay accordingly, as it will otherwise be on top of your other HTML elements which can be in the same `BrowserWindow`.

You can set a padding with
```JavaScript
osn.NodeObs.OBS_content_setPaddingSize(displayId, 5);
osn.NodeObs.OBS_content_setPaddingColor(displayId, 255, 255, 255);
```
