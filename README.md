# Phaser Manifest Loader for use with Webpack

![PhaserManifestLoader](https://raw.githubusercontent.com/mattcolman/phaser-manifest-loader/master/assets/images/phaser-manifest-loader.jpg)

## TL;DR
Phaser Manifest Loader means you can load assets that have been compressed and fingerprinted by webpack as easily as this:
```
const manifest = {
  spritesheets: [
    'creature1',
    'creature2'
  ],
  'bitmap_fonts': [
    'bitmapfont'
  ],
  'audio': [
    'blip'
  ],
  'images': [
    'phaser_logo',
    'webpack_logo'        
  ],
  fonts: {
    custom: {
      families: [
        'bebas_neuebold'
      ]
    },
    google: {
      families: [
        'Bangers'
      ]
    }
  }
}
```

## The Problem
Webpack is great!
Webpack loaders are great because they compress and fingerprint your assets for you. Without this you cannot change an image on production and guarantee your users will see the new image as it may be stuck in a user's browser cache.
However, when you use webpack loaders your main state can end up looking like this:
```
import creature1Image from '../assets/spritesheets/creature1.png'
import creature1Json from '../assets/spritesheets/creature1.json'
import blipSound from '../assets/audio/blip.mp3'
...

preload() {
  this.game.load.atlasJSONArray('creature1', creature1Image, null, creature1Json)
  this.game.load.audio('blip', blipSound)
  ...
}
```
...and before you even start writing your game code you already have 100 lines of code!
Also I always have to look up the Phaser docs to remember how to load assets...it takes time!

## The Solution
*Phaser Manifest Loader* provides an easy way to load assets.
After the initial setup, you just drop a new image into the images folder and add it to the manifest, or a new png / json combo into the spritesheets folder and add it to the manifest and the assets will appear in the cache ready for use in your game.

## Who should use Phaser Manifest Loader?
Anyone that uses Phaser and webpack may benefit from this.
I think it will be particularly useful for teams of developers as it provides convention and simplicity to asset loading.
It will be very easy for example to explain to new junior developers how to load in assets.
```
1. Drop your asset into the appropriate folder.
2. Include it in the manifest.
```
Easy!
Rather than explaining how webpack works and referring them to both the webpack and phaser docs.

## Features
- supports scaling variants for multi resolutions.
The suffix is the 2nd parameter of the loadManifest method.
`this.game.plugins.add(ManifestLoader).loadManifest(manifest, '_2x')`
- filetypes
  - jpg
  - png
  - atlasJSONArray(png + json)
  - mp3
  - bitmapfonts(png + xml)
  - web fonts
  - more to come
- Webfonts are also built in so you don't have to mess around with logic like:
```
render() {
  if (this.webfontsLoaded) {
    this.game.state.start('main')
  }
}
```

## Install
`yarn add phaser-manifest-loader` OR
`npm i phaser-manifest-loader --save`

## Setup
1. First create an assets directory with subdirectories:
  - images
  - spritesheets
  - audio
  - fonts
  - bitmap_fonts


## Simple Usage
```
import { AssetLoader } from 'phaser-manifest-loader' // AssetLoader has the same api as ManifestLoader is more lightweight because it doesn't include webfontloader

import manifest from './manifest' // see manifest example below

// tell webpack where your assets directory is using require.context
const req = require.context('../assets');

// no webfonts in this manifest so we can simply use the preload method
preload() {  
  this.game.plugins.add(AssetLoader, req).loadManifest(manifest)
}
```

## Advanced Usage (Webfonts)
```
import '../assets/fonts/bebas/stylesheet.css' // IMPORTANT remember to load your webfont stylesheet
import ManifestLoader from 'phaser-manifest-loader'
import manifest from './manifest' // see manifest example below

// a more advanced way to use require.context
const req = require.context('../assets', true, /.*\.png|json|ttf|woff|woff2|xml|mp3|jpg|jpeg$/);

// Load in a manifest of assets including web fonts
// because webfonts don't use the Phaser loader we can't take advantage of Phaser's
// preload method. So we performing loading in the create method and use the Promise
// returned from `loadManifest`.
create() {
  const loader = this.game.plugins.add(ManifestLoader, req)
  loader.loadManifest(manifest).then(() => {
    this.state.start('Main');
  })
}
```

## Manifest Example
```
const manifest = {
  spritesheets: [
    'creature1',
    'creature2'
  ],
  'bitmap_fonts': [
    'bitmapfont'
  ],
  'audio': [
    'blip'
  ],
  'images': [
    'phaser_logo',
    'webpack_logo'        
  ],
  fonts: {
    custom: {
      families: [
        'bebas_neuebold'
      ]
    },
    google: {
      families: [
        'Bangers'
      ]
    }
  }
}
export default manifest
```

## API
### loadManifest

[loadManifest loads a manifest of assets]

**Parameters**

-   `manifest` **{spritesheets: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>, images: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>, audio: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>, bitmap_fonts: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>, fonts: {custom: {families: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>}, google: {families: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)>}}}**
-   `assetPostfix` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)?**  (optional, default `''`)

Returns **[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)**

## Run demo
`npm start`

## Build demo
`npm run build-demo`

### Credits
https://github.com/lean/phaser-es6-webpack
