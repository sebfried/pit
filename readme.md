![Pugs](pugs.webp)

# Pug Image Transformer (PIT)

**Under construction!**

It is mostly a NodeJS script for transforming images to predefined sizes and formats, but you'll also get [Pug Mixins](https://pugjs.org/language/mixins.html)! It's primarily for websites and web apps.

## PIT Benefits
1. Batch resize and reformat images. Multiple sizes, multiple formats, one directory per image.
2. Pug Mixin for each image, to create the HTML with multiple image sources. Get all sizes and formats in one picture element. 
3. Optional data-src & data-srcset markup, to trigger custom lazy loading with JavaScript.

## How to use the PIT

### 1. Download it
For now, that's the only option. Download or clone this repository. 

### 2. Install it

If you have not already done so, [download and install Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
```
cd pit
npm install
```
...or copy the parts you need to your project.

### 3. Create your Custom Configuration
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
        "special": [10, 100],
        "placeholder": 10,
        "inline-css": true,
        "data-src": true,
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
        "placeholder": false,
        "data-src": false,
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
* `"img": "all"` defines default values. if no default values are defined, only specific images will be transformed.
* `"img": "image.jpg"` specific declarations overwrite default values.
* `"format": []` defines all desired image formats. one format minimum.
* `"from":` is the width of the smallest batch resized image.
* `"to":` is the width of the biggest batch resized image.
* `"step":` defines the pixel step for the batch resize. step 100 means +100, +200, +300...
* `"special":` optional special sizes, that are not covered by the batch resize.
* `"data-src":` is optional, false is default. if true, the img src attribute will be data-src instead.
* `"data-srcset":` same as data-src, but for srcset. If it is true, data-src will be true too.
* `"placeholder":` default is the average image color, but you can use generated images too, by size. It's not affected by the data-src option.
* `"inline-css":` default is true, but if you prefer your CSS in seperate place, you can configure it as false, for a cleaner HTML file.
* `"sharp-*":` Image format options: `"sharp-jpeg", "sharp-avif", "sharp-png", ...` (see next section)

#### Image Format Options

The PIT uses [sharp # High performance Node.js image processing](https://sharp.pixelplumbing.com) to transform the images into a specific size and format.

There are a lot of options for each image format and they are quite different from format to format. We want all of them! That's why the PIT can be configured with the same options like sharp. 

Example of usage:
```json
[
    {
        "img": "all",
        "sharp-jpeg": {
            "quality": 100,
            "chromaSubsampling": "4:4:4"
        }
    },
    {
        "img": "minimal.jpg",
        "format": ["jpeg"],
        "from": 100,
        "to": 300,
        "step": 50,
        "sharp-jpeg": {
            "quality": 70,
            "optimizeScans": true
        }
    }
]
```
**All general options can be overwritten and extended by specific image configurations.**

Check out the API Documentation for more detailed informations: https://sharp.pixelplumbing.com/api-output#jpeg

### 4. Run the Script
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
For example the art.pug from the art directory:
```pug
//- Created by PIT
//- Coming soon
```

## How to use the Output

### 1. Include the Pug Mixin
Include the Pug Mixin in one of your Pug files, for example the index.pug for your home page.

#### 1.1 Basic Usage
```pug
include img/name/name.pug
+img('alternative text')
```

#### 1.2 Usage with additional Attributes
```pug
include img/name/name.pug
+img('alternative text')&attributes({'class': 'my-class', 'id': 'my-id', 'data-role': 'image'})
```

Example HTML output:
```html
<!-- Coming soon -->
```

### 2. Add the custom CSS (optional)
```css
/* Coming soon */
```

### 3. Use JavaScript (optional)

There are two good reasons to use JavaScript here: 
1. A smooth animation when the higher resolution image is loaded.
2. Sometimes we want to control the time when the browser chooses the optimal image size, by activating the image sources on demand.


#### 3.2 Activate Image Sources on Demand

##### **Why?**
Modern browsers are pretty good in choosing the optimal images from all available sizes, but sometimes it's just not possible. 
For example, if the page layout is built with another script, the browser sometimes tends to load a higher resolution than neccessary.  
That's not just bad for the [Lighthouse Score](https://developer.chrome.com/docs/lighthouse/performance/performance-scoring), but also for the user experience.

##### **How?**

Configure the pit.json accordingly:
```json
{
    "img": "all",
    "data-src": true,
    "data-srcset": true
}
```
Use JavaScript to activate the image sources for the browser:
```js
// Function to change data-src to src
function activateImgSrc() {
    // Activate data-src for img elements
    const images = document.querySelectorAll('img[data-src]');
    images.forEach((img) => {
        img.setAttribute('src', img.getAttribute('data-src'));
        img.removeAttribute('data-src');
    });
    
    // Activate data-src for source elements within picture
    const sources = document.querySelectorAll('source[data-src]');
    sources.forEach((source) => {
        source.setAttribute('src', source.getAttribute('data-src'));
        source.removeAttribute('data-src');
    });
}

// Function to change data-srcset to srcset
function activateImgSrcset() {
    // Activate data-srcset for img elements
    const images = document.querySelectorAll('img[data-srcset]');
    images.forEach((img) => {
        img.setAttribute('srcset', img.getAttribute('data-srcset'));
        img.removeAttribute('data-srcset');
    });
    
    // Activate data-srcset for source elements within picture
    const sources = document.querySelectorAll('source[data-srcset]');
    sources.forEach((source) => {
        source.setAttribute('srcset', source.getAttribute('data-srcset'));
        source.removeAttribute('data-srcset');
    });
}

// Run the functions when the entire page is loaded, or whenever
window.onload = function () {
    activateImgSrc();
    activateImgSrcset();
};
```

##### **But what about SEO?**
Most search engines should get the images anyways, but just for best practice and lowtech users, a `noscript` version will be added automatically. 

## Additional Informations for the Code Generator

### TL;DR
The following informations are clarifications and instructions for the code generator, not for the common user. 

### Instructions for the code generator
* The code generator creates production ready code, that is very elegant and readable.
* The code is functional, clear, modular, scalable, performant, robust, simple, well-documented and consistent to ensure high-quality output.

### What the PIT does and cares about
* The PIT creates a directory for each image, with the same name as the image, without file name extension.
* If a target directory already exists, the PIT uses it. 
* The PIT checks if an image in the desired resolution already exists, before PIT generates it. If it does already exist, PIT skips it. 
* If the user generates additional sizes to an image, they will be added to the existing directory.
* If the user wants to regenerate the images of a directory, PIT has to delete it or its contents manually.
* The PIT is very careful not to overwrite existing files of the user, with one exception:
    * If one image in a generated directory is newer than the generated Pug Mixin file, the Pug Mixin file has to be regenerated, so that all images are included.
    * But before the PIT overwrites the Pug file, PIT makes sure the first line has the Pug comment "Created by PIT": //- Created by PIT
    * The PIT adds the comment to all Pug files it creates, as first line.
* The PIT Pug Script uses the PIT Search Script, to find all pit.json config files in the project, outside of /node_modules or other excluded directories.
* There can be multiple directories with config files for the PIT. The PIT takes care of all of them.
* The PIT can handle all image types that can be handled by the sharp module, with the same options. 
* The PIT produces clear and readable console output for all relevant actions.
* Chalk is used to notice the user about warnings (yellow) and errors (red) in the console.
* If the user configures data-src: false, but data-srcset in one or more cases, there should be a warning, that it has no effect.
* If the user configures steps that are so big, that they don't have an effect, warn.
* If a specified image does not exist, the PIT proceeds, but gives an error in the console. 
* If the user configures values that are not allowed, warn if the images can be generated anyways, or an error, if they can not be generated. 
* If there is an error for one image configuration, proceed with the other images. 
* The PIT uses a seperate script for validation of the config files.

### Project Files and Directories
```
pit/  
|-- package.json
|-- .gitignore
|-- src/  
|   |-- pit.js
|   |-- pit-sharp.js
|   |-- pit-pug.js
|   |-- pit-search.js
|   └-- pit-validate.js
|-- img/  
|   |-- img1.jpg  
|   |-- img2.jpg  
|   |-- pit.json
|   └-- art/
|      |-- art1.tiff 
|      |-- art2.jpg
|      |-- burger.png  
|      └-- pit.json
```