# Docs
## Example Projects
- [obs-studio-node-example](https://github.com/Envek/obs-studio-node-example/), a minimalistic screen and webcam recording example. This documentation is heavily based on their work. 
- [AGView](https://github.com/hrueger/AGView), a slide-based presenting software. It's a little more complex than [obs-studio-node-example](https://github.com/Envek/obs-studio-node-example/) becuase it uses TypeScript and Angular, but all the `obs-studio-node` code lives in [this file](https://github.com/hrueger/AGView/blob/master/src/worker/obs.ts).

## Table of Contents
1. Initialization and Shutdown (you are here, see below)
2. [Configuration](./configuration.md)
3. [Scenes and Sources](./scenes-and-sources.md)
4. [Transitions](./transitions.md)
5. [Recording](./recording.md)
6. [Displays](./displays.md)

## Usage
> **Important:** If used with Electron, everything shown here has to be done in the **main** process, not in the renderer process. If you get `Uncaught Error: Failed to host and connect`, this could be the problem.

### Import `obs-studio-node`
TypeScript:
```TypeScript or ES2015
import * as osn from "obs-studio-node";
```
JavaScript:
```JavaScript
const osn = require("obs-studio-node");
```

### Initialization
```JavaScript
const { Subject } = require("rxjs");
// Usually some UUIDs go there
osn.NodeObs.IPC.host("obs-studio-node-example");
// set the working dir
// when packaged, the osn folder is unpacked from electron's asar file
// so we have to fix the path by replacing `app.asar` with `app.asar.unpacked`
osn.NodeObs.SetWorkingDirectory(path.join(__dirname, "../../node_modules/obs-studio-node").replace("app.asar", "app.asar.unpacked"));
// OBS Studio configs and logs
const obsDataPath = path.join(__dirname, "../../osn-data").replace("app.asar", "app.asar.unpacked"); 
// init api with locale, data path and version
const initResult = osn.NodeObs.OBS_API_initAPI("en-US", obsDataPath, "1.0.0");

if (initResult !== 0) {
  const errorReasons = {
    "-2": "DirectX could not be found on your system. Please install the latest version of DirectX for your machine here <https://www.microsoft.com/en-us/download/details.aspx?id=35?> and try again.",
    "-5": "Failed to initialize OBS. Your video drivers may be out of date, or Streamlabs OBS may not be supported on your system.",
  };

  const errorMessage = errorReasons[initResult.toString()] || `An unknown error #${initResult} was encountered while initializing OBS.`;

  console.error("OBS init failure", errorMessage);

  // see below for this function
  shutdown();

  throw Error(errorMessage);
}

// obs-studio-node sends output signals
// Using them, we can determine, if a function was
// executed correctly. For example, when we tell 
// osn to stop recording, we don't know if that was
// successfull until we receive a recording stopped
// singal. The function below makes those signals
// easier to use.
const signals = new Subject()
osn.NodeObs.OBS_service_connectOutputSignals((signalInfo) => {
    signals.next(signalInfo);
});
function getNextSignalInfo() {
  return new Promise((resolve, reject) => {
    signals.pipe(first()).subscribe(signalInfo => resolve(signalInfo));
    setTimeout(() => reject('Output signal timeout'), 30000);
  });
}
```

### Shutting down
```JavaScript
function shutdown() {
  try {
    osn.NodeObs.OBS_service_removeCallback();
    osn.NodeObs.IPC.disconnect();
  } catch (e) {
    throw Error(`Exception when shutting down OBS process${e}`);
  }
}
```
