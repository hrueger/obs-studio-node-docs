# Recording
## Start
```JavaScript

function getNextSignalInfo() {
  return new Promise((resolve, reject) => {
    signals.pipe(first()).subscribe(signalInfo => resolve(signalInfo));
    setTimeout(() => reject('Output signal timeout'), 30000);
  });
}
let signalInfo;
console.debug('Starting recording...');
osn.NodeObs.OBS_service_startRecording();
console.debug('Started?');
signalInfo = await getNextSignalInfo();
if (signalInfo.signal === 'Stop') {
  throw Error(signalInfo.error);
}
console.debug('Started signalInfo.type:', signalInfo.type, '(expected: "recording")');
console.debug('Started signalInfo.signal:', signalInfo.signal, '(expected: "start")');
console.debug('Started!');
```

## Stop
```JavaScript
let signalInfo;
console.debug('Stopping recording...');
osn.NodeObs.OBS_service_stopRecording();
console.debug('Stopped?');
signalInfo = await getNextSignalInfo();
console.debug('On stop signalInfo.type:', signalInfo.type, '(expected: "recording")');
console.debug('On stop signalInfo.signal:', signalInfo.signal, '(expected: "stopping")');
signalInfo = await getNextSignalInfo();
console.debug('After stop signalInfo.type:', signalInfo.type, '(expected: "recording")');
console.debug('After stop signalInfo.signal:', signalInfo.signal, '(expected: "stop")');
console.debug('Stopped!');
```
