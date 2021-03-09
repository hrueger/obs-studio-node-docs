# Configuration for recording
```JavaScript
function getAvailableValues(category, subcategory, parameter) {
  const categorySettings = osn.NodeObs.OBS_settings_getSettings(category).data;
  if (!categorySettings) {
      console.warn(`There is no category ${category} in OBS settings`);
      return [];
  }

  const subcategorySettings = categorySettings.find(
      (sub) => sub.nameSubCategory === subcategory,
  );
  if (!subcategorySettings) {
      console.warn(`There is no subcategory ${subcategory} for OBS settings category ${category}`);
      return [];
  }

  const parameterSettings = subcategorySettings.parameters.find(
      (param) => param.name === parameter,
  );
  if (!parameterSettings) {
      console.warn(`There is no parameter ${parameter} for OBS settings category ${category}.${subcategory}`);
      return [];
  }

  return parameterSettings.values.map((value) => Object.values(value)[0]);
}

function setSetting(category, parameter, value) {
  let oldValue;
  // Getting settings container
  const settings = osn.NodeObs.OBS_settings_getSettings(category).data;

  settings.forEach((subCategory) => {
    subCategory.parameters.forEach((param) => {
      if (param.name === parameter) {
        oldValue = param.currentValue;
        param.currentValue = value;
      }
    });
  });
  // Saving updated settings container
  if (value != oldValue) {
    osn.NodeObs.OBS_settings_saveSettings(category, settings);
  }
}

setSetting("Output", "Mode", "Simple");
const availableEncoders = getAvailableValues("Output", "Recording", "RecEncoder");
setSetting("Output", "RecEncoder", availableEncoders.slice(-1)[0] || "x264");
setSetting("Output", "FilePath", "/videos");
setSetting("Output", "RecFormat", "mkv");
setSetting("Output", "VBitrate", 10000); // 10 Mbps
setSetting("Video", "FPSCommon", 60);
```