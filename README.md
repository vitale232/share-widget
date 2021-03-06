# share-widget

Share widget built for version 4.x of the ArcGIS API for Javascript

![share-widget](https://github.com/Esri/share-widget/blob/master/images/share-link.png?raw=true)​

This repository is a fork of the original with some added tooling.

## Why the Fork?

The Esri JS API custom widget framework is pretty well documented, but there are two things missing from my perspective.

1. `ViewModel` examples
2. Build examples

The JS API makes heavy usage of `ViewModels`, and their advantages to end users are quite apparent. However, if you're looking to develop a custom widget that includes a `ViewModel`, the only good example I've found is the Share widget. The official documentation merely makes mention of the `ViewModel` architecture, and a quick summary of the benefits to the developer.

the Esri Share widget repository provides some great Widget source code, but there's not many clues on how to build the TypeScript and load the widget up into a Map. There is one closed issue in the GitHub repo that helps point out some of the imports, but I found that a substantial amount of configuration for TypeScript's compilerOptions was necessary.

This fork aims to address issue 2, by providing a repeatable method of building the Esri Share Widget using NPM. By solving issue 2, we hopefully help to solve issue 1.

## Building the Widget

To build the widget, start by cloning this repository. Make sure you have Node.js installed on your computer, then run the following command from the root of this repository:

```shell
npm install
```

That will install the necessary dependencies and development dependencies to get the `index.html` file to load successfully.

Once the dependencies are installed, you'll want to compile the Sass files into CSS so that they can be consumed by the browser. You'll also need to compile the TypeScript into JavaScript files. This repo includes NPM scripts in the `package.json` file to make these tasks simple.

```shell
npm run build-css
npm run build-widget
```

Once the files are stored, you can use the npm `http-server` package to serve up `index.html`. This is necessary because the Share Widget sends the URL of the page off to an Esri server to be shortened, making it more shareable. The Esri server expects to receive a URL (that starts with http:// or https://), not a filepath. By using the http-server, we avoid errors from the Share Widget's `ViewModel`.

To serve the page, run the following command from the root directory of this repo:

```shell
http-server
```

Then, navigate in your browser to http://127.0.0.1:8081/index.html and you should see a map of the continental United States, with the Esri Share widget in the bottom right corner of the map.

Note that there is also a convenience script that will recompile the TypeScript every time a TS file is updated. You can run the following command to have the files watched:

```shell
npm run watch-build-widget
```

You will still need to refresh the browser to see the latest updates.

## Features:

1.  `MapView` and `SceneView` compatability
2.  Share map via URL
3.  Embed map with Iframe code
4.  Share item services, i.e. social media or e-mail
5.  Client-side projection for non-Web Mercator/non-WGS84 spatial references
6.  Customizable Share Items
    - _Default items: Facebook, Twitter, LinkedIn, and Email_
7.  Customizable Share Features (All features toggled on by default):
    - Copy URL to clipboard
    - Share Services, i.e. Social Media or Email
    - Shorten URL
    - Embed Map

**\*Note:** Share Widget uses Esri's Calcite CSS Styles.\*

Calcite Web Documentation: https://esri.github.io/calcite-web/documentation/

## ShareWidget

### Constructor:

#### new **ShareWidget(_properties?_)**

##### Property Overview:

| Name             | Type                    | Summary                                     |
| ---------------- | ----------------------- | ------------------------------------------- |
| iconClass        | String                  | The widget's default CSS icon class.        |
| label            | String                  | The widget's default label.                 |
| shareFeatures    | ShareFeatures           | A reference to `ShareFeatures`.             |
| shareItems       | Collection\<ShareItem\> | Collection of `ShareItem` class.            |
| shareModalOpened | Boolean                 | Property to toggle share modal              |
| shareUrl         | String                  | Share URL of web application. `Read-only`   |
| viewModel        | ShareViewModel          | The view model for this widget.             |
| view             | MapView \| SceneView    | A reference to the `MapView` or `SceneView` |

## ShareItem

### Constructor:

#### new **ShareItem(_properties?_)**

##### Property Overview:

| Name        | Type   | Summary                                                                                                                                             |
| ----------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| className   | String | Class name of share item icon.                                                                                                                      |
| id          | String | Unique ID of share item service i.e. `facebook`, `twitter`, `linkedin`, `email`, etc.                                                               |
| name        | String | Name of Share Item.                                                                                                                                 |
| urlTemplate | String | URL template of share item. Consists of share service URL along with URL parameters i.e `"https://pinterest.com/pin/create/bookmarklet?&url={url}"` |

## ShareFeatures

### Constructor:

#### new **ShareFeatures(_properties?_)**

##### Property Overview:

| Name            | Type    | Summary                                      |
| --------------- | ------- | -------------------------------------------- |
| copyToClipboard | Boolean | Toggles copy share URL to clipboard feature. |
| embedMap        | Boolean | Toggles embed map feature.                   |
| shareServices   | Boolean | Toggles share item services feature.         |
| shortenLink     | Boolean | Toggles shorten URL feature.                 |

**Please note:** Both `copyToClipboard` and `shareServices` properties **cannot** be toggled off concurrently.

## ShareViewModel

### Constructor:

#### new **ShareViewModel(_properties?_)**

##### Property Overview:

| Name             | Type                    | Summary                                     |
| ---------------- | ----------------------- | ------------------------------------------- |
| embedCode        | String                  | IFrame Embed Code `Read-only`.              |
| shareFeatures    | ShareFeatures           | A reference to `ShareFeatures`.             |
| shareItems       | Collection\<ShareItem\> | Collection of `ShareItem` class.            |
| shareModalOpened | Boolean                 | Property to toggle share modal              |
| shareUrl         | String                  | Share URL of web application. `Read-only`   |
| state            | String                  | Current state of the widget.                |
| view             | MapView \| SceneView    | A reference to the `MapView` or `SceneView` |

### **Examples:**

##### Default:

```
const share = new ShareWidget({
    view,
    container: document.createElement("div")
});
```

##### Custom:

```
  const PINTEREST_ITEM = new ShareItem({
    id: "pinterest",
    name: "pinterest",
    className: "icon-social-pinterest",
    urlTemplate:
      "https://pinterest.com/pin/create/bookmarklet?&url={url}"
  });
  const REDDIT_ITEM = new ShareItem({
    id: "reddit",
    name: "Reddit",
    className: "icon-social-share",
    urlTemplate: "https://reddit.com/submit?url={url}"
  });
  const LINKED_IN = new ShareItem({
    id: "linkedin",
    name: "LinkedIn",
    className: "icon-social-linkedin",
    urlTemplate: "https://linkedin.com/shareArticle?url={url}"
  });

  const shareFeatures = new ShareFeatures({
    copyToClipboard: false,
    embedMap: false
  });
  const ShareItemCollection = Collection.ofType<ShareItem>(ShareItem);
  const shareItems = new ShareItemCollection([
    PINTEREST_ITEM,
    REDDIT_ITEM,
    LINKED_IN
  ]);
  const share = new ShareWidget({
    view,
    shareFeatures,
    shareItems
  });
```

## Resources

- [ArcGIS for JavaScript API Resource Center](http://help.arcgis.com/en/webapi/javascript/arcgis/index.html)
- [ArcGIS Blog](http://blogs.esri.com/esri/arcgis/)
- [twitter@esri](http://twitter.com/esri)

## Issues

Find a bug or want to request a new feature? Please let us know by submitting an issue.

## Contributing

Esri welcomes contributions from anyone and everyone. Please see our [guidelines for contributing](https://github.com/esri/contributing).

## Licensing

Copyright 2018 Esri

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

A copy of the license is available in the repository's [LICENSE](https://github.com/ArcGIS/share-widget/blob/master/LICENSE) file.
