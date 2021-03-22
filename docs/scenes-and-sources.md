# Scenes and Sources
## Creating
You can create a scene like so:
```JavaScript
const scene = osn.SceneFactory.create("myScene");
```
Then, you can create a source and add it:
```JavaScript
const source = osn.InputFactory.create("image_source", "logo", { file: path.join(__dirname, "../assets/icons/favicon.png") });
const sceneItem = scene.add(source);
```

The `osn.InputFactory.create()` method needs the parameters `id`, `name` and optionally `settings`. The `id` could for example be `image_source`, `browser_source`, `ffmpeg_source` or `text_gdiplus`. In the settings object you can specify `is_local_file: true` and `local_file: "myPathToTheFile"`. For videos, you can say `looping: true`. The settings for `text_gdiplus` look different: `read_from_file: true, file: "myPathToTheFile"`.

## Audio for `ffmpeg_sources`
If you have a `ffmpeg_source`, you can set the `monitoringType` to one of the following values:
```TypeScript
// to not output any sound
s.monitoringType = osn.EMonitoringType.None; // is the same as s.monitoringType = 0;
// to hear it but not have it on the recording or stream
s.monitoringType = osn.EMonitoringType.MonitoringOnly; // is the same as s.monitoringType = 1;
// to hear it and record or stream it
s.monitoringType = osn.EMonitoringType.MonitoringAndOutput; // is the same as s.monitoringType = 2;
```

## Finding
If you know the name of any object (for example a scene or a source), you can get it with the `[factory].fromName(name)`. For example:
```JavaScript
const myScene = osn.SceneFactory.fromName("myScene");
```

## Working with scene items
You can modify the properties of the scene item to for example move or scale the source:
```JavaScript
sceneItem.position = { x: 50, y: 50 };
sceneItem.scale = { x: 0.5, y: 0.7 };
```

## Setting the scene as an output
```JavaScript
osn.Global.setOutputSource(0, scene);
```