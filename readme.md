![Pugs](pugs.webp)

# Pug Image Transformer (PIT)

It is mostly a NodeJS script for transforming images to predefined sizes and formats, but you'll also get [Pug Mixins](https://pugjs.org/language/mixins.html)! It's primarily for websites and web apps.

## PIT Benefits
1. Batch resize and reformat images. 
    Multiple sizes, multiple formats, one directory per image.
2. Pug Mixin for each image, to create the HTML with the multiple sources. Get all sizes and formats in one picture element. 
3. Optional data-src & data-srcset markup, to trigger custom lazy loading with JavaScript.

## How to use the PIT

### Install it
```
cd pit
npm install
```
...or copy the parts you need to your project.

### Create your Custom Configuration
PIT looks for files with the name `pit.json`. That's the configuration file for the image directory.
```
cd img/
touch pit.json
```

Example configuration in `pit.json`:
```json
[
    {
        "img": "all",
        "format": ["webp", "avif"],
        "from": 600,
        "to": 2000,
        "step": 300,
        "special": [1, 10, 100],
        "quality": 80,
        "data-srcset": true
    },
    {
        "img": "minimal.jpg",
        "format": ["webp"],
        "from": 100,
        "to": 300,
        "step": 50
    },
    {
        "img": "image.jpg",
        "format": ["avif"],
        "from": 800,
        "to": 3000,
        "step": 200,
        "special": [2, 10, 50],
        "data-src": true,
        "sharp-avif": {
            "quality": 70,
            "effort": 7
        }
    },
    {
        "img": "art.tiff",
        "format": ["jpg","avif"],
        "from": 1000,
        "to": 1200,
        "step": 150,
        "sharp-avif": {
            "quality": 80,
            "effort": 8
        },
        "sharp-jpeg": {
            "optimizeScans": true
        }
    }
]
```
* `"img": "all"` defines default values. if no default values are defined, only specific images will be tranformed.
* `"img": "image.jpg"` specific declarations overwrite default values.
* `"format": []` defines all desired image formats. one format minimum.
* `"from":` is the width of the smallest batch resized image.
* `"to":` is the width of the biggest batch resized image.
* `"step":` defines the pixel step for the batch resize. step 100 means +100, +200, +300...
* `"special":` optional special sizes, that are not covered by the batch resize.
* `"data-src":` is optional, false is default. if true, the img src attribute will be data-src instead.
* `"data-srcset":` same as data-src, but for img srcset.
* `"sharp-*":` ...

### sharp-*
The PIT uses [sharp # High performance Node.js image processing](https://sharp.pixelplumbing.com) to transform the images into a specific size and format.

There are a lot of options for each image format and they are quite different from format to format. We want all of them! That's why the PIT can be configured with the same options like sharp. 

Check out the API Documentation for more detailled informations: https://sharp.pixelplumbing.com/api-output#jpeg

### Run the Script
```
npm start
```
... or have more control with optional npm commands:

### Optional npm Commands
* Alternative to npm start: `npm run pit`
* Transform images only, no pug: `npm run pit:img`
* Create Pug files only, no image transformation: `npm run pit:pug`

## Example Output
### Directory View
```
img/  
|-- art.jpg  
|-- art/  
|   |-- art-1000.jpg  
|   |-- art-1000.avif  
|   |-- art-1150.jpg  
|   |-- art-1150.avif  
|   |-- art-1200.jpg  
|   |-- art-1200.avif  
|   └-- art.pug
...
```

### Pug Mixin
```pug
//- Created by PIT
//- Coming soon
```

## How to use the Pug Mixin

## Basic Usage
```pug
include img/name/name.pug
+img('Information')
```

## With additional attributes
```pug
include img/name/name.pug
+img('Information')&attributes({'class': 'my-class', 'id': 'my-id', 'data-role': 'image'})
```

## How to use the JavaScript (data-*)
Sometimes we want the option to delay the time when the browser chooses the optimal image size. That's where this JavaScript code can be used, in combination with the data-* options in the config. 

```js
    function updateImageSrc() {
        const images = document.querySelectorAll('img[data-src]');
        images.forEach((img) => {
            img.setAttribute('src', img.getAttribute('data-src'));
            img.removeAttribute('data-src');
        });
    }

    function updateImageSrcSet() {
        const images = document.querySelectorAll('img[data-srcset]');
        images.forEach((img) => {
            img.setAttribute('srcset', img.getAttribute('data-srcset'));
            img.removeAttribute('data-srcset');
        });
    }

    window.onload = function () {
        updateImageSrc();
        setTimeout(updateImageSrcSet, 1000);
    };
```

## Additional Informations for the Code Generator

### What the PIT does and cares about
* The PIT creates a directory for each image, with the same name as the image, without file name extension.
* If the directory already exisists, the PIT uses it. 
* The PIT checks if an image in the desired resolution already exists, before he generates it. If it does already exist, he skips it. 
* If the user generates additional sizes to an image, they will be added to the existing directory.
* If the user wants to regenerate the images of a directory, he has to delete it or its contents manually.
* The PIT is very careful not to overwrite existing files of the user, with one exception.
    * If one image in a generated directory is newer than the generated Pug Mixin file, the Pug Mixin file has to be regenerated, so that all images are included.
    * But before the PIT overwrites the Pug file, he makes sure the the first line has the Pug comment "Created by PIT". //- Created by PIT
    * The PIT adds the comment to all Pug files he creates, as first line.
* The PIT Pug Script uses the the PIT Search Script, to find all pit.json config files in the project, outside of /node_modules or other exclude directories.
* There can be multiple directories with config files for the PIT. The PIT takes care of all of them.
* The PIT can handle all image types that can be handled by the sharp module, with the same options. 

### Project Files and Directories
```
pit/  
|-- package.json
|-- .gitignore
|-- img/  
|   |-- img1.jpg  
|   |-- img2.jpg  
|   |-- pit.json
|   └-- art/
|      |-- art1.tiff 
|      |-- art2.jpg
|      |-- burger.png  
|      └-- pit.json
|-- scripts/  
|   |-- pit.mjs
|   |-- pit-sharp.mjs
|   |-- pit-pug.mjs
|   └-- pit-search.mjs
...
```