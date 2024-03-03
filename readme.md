![Pugs](pugs.webp)

# Pug Image Transformer  (PIT)

**This is the fantastic ideal specification/documentation of the npm module `pugsharp`, that is developed here: [pugsharp](https://github.com/sebfried/pugsharp)**

## About

It is mostly a Node.js module to transform images into predefined sizes and formats, but you'll also get [Pug Mixins](https://pugjs.org/language/mixins.html) for your web project!


## PIT Benefits
1. Batch resize and reformat images. Multiple sizes, multiple formats, one directory per image.
2. Pug Mixin Template for each image, to create the HTML with multiple image sources. Get all sizes and formats in one picture element. 
3. Optional data-src & data-srcset markup, to trigger custom lazy loading with JavaScript.

## Cred
> « *The PIT is the Node.js module that Pug users have been waiting for,  
> making responsive image handling a breeze in web projects.* »  
>
> – Grimoire Custom GPT

## How to use the PIT?

### 1. Download it
For now, that's not really an option. Try [pugsharp](https://github.com/sebfried/pugsharp) instead.

### 2. Install it

If you have not already done so, [download and install Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
```
cd pit
npm install
```
...or copy the parts you need to your project.

### 3. Create your Custom Configuration
PIT looks for files with the name `pit.json`. That's the configuration file for a image directory.
```
cd img/
touch pit.json
```

Example configuration in `pit.json`:
```json
[
    {
        "img": "default",
        "format": ["webp", "avif"],
        "special": [1, 10],
        "placeholder": 10,
        "inline-css": true,
        "pug-to-html": false,
        "data-src": true,
        "data-srcset": true
    },
    {
        "img": "all",
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
        "img": ["art.tiff", "bart.gif"],
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
* `"img":` 
    * `"img": "default"` defines default values for the whole directory. 
    * `"img": "all"` defines values for all (remaining) images in the directory. If it is absent, only declared images will be processed.
    * `"img": ["all", "default"]` means that all remaining images will be processed with the default values. 
    * `"img": "image.jpg"` for single images. declarations overwrite the default values for this image.
    * `"img": ["image1.jpg", "image2.png"]` to use the same specific config for more than one image.
    * `"img": ["image1.jpg", "image2.png", "default"]` is also possible.
* `"format":` defines all desired image formats. takes values or arrays
    * `"format": ["jpg", "png"]` creates JPG and PNG files for the images.
    * `"format": "same"` means the same as the file name extension of the source image. 
* `"from":` is the width of the smallest batch resized image.
* `"to":` is the width of the biggest batch resized image.
* `"step":` defines the pixel step for the batch resize. step 100 means +100, +200, +300...
* `"special":` optional special sizes, that are not covered by the batch resize. takes numbers or arrays.
    * `"special": 1` one pixel width.
    * `"special": [1, 10, 100]` creates three special sizes, by pixel width.
* `"data-src":` is optional, false is default. if true, the img src attribute will be data-src instead.
* `"data-srcset":` same as data-src, but for srcset. If it is true, data-src will be true too.
* `"placeholder":` default is the average image color, but you can use generated images too, by size. It's not affected by the data-src option.
* `"inline-css":` default is true, but if you prefer the CSS in a seperate place, you can configure it as false, for a cleaner HTML file.
* `"pug-to-html":` default is true. It creates the HTML snippets as img.html next to the img.pug. If you don't need it, deactivate it.
* `"sharp-*":` Image format options: `"sharp-jpeg", "sharp-avif", "sharp-png", ...` (see next section)

#### Image Format Options

The PIT uses "[sharp # High performance Node.js image processing](https://sharp.pixelplumbing.com)" to transform the images into a specific size and format.

There are a lot of options for each image format and they are quite different from format to format. We want all of them! That's why the PIT can be configured with the same options like sharp. 

Example of usage:
```json
[
    {
        "img": "default",
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
**All default options can be overwritten and extended by specific image configurations.**

Check out the [sharp API Documentation](https://sharp.pixelplumbing.com/api-output) for more detailed informations about the available image format options.

### 4. Run the Script
```
npm start
```
... or have more control with optional npm commands:

#### Optional npm Commands
* Alternative to npm start: `npm run pit`
* Transform images only, no pug: `npm run pit:img`
* Create Pug files only, no image transformation: `npm run pit:pug`

## Example Output
### Directory View Example
```
img/  
|-- pit.json
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

### Pug Mixin Example
The art.pug from the art directory:
```pug
//- Created by PIT
mixin landscape(altText="")
    //- Configuration variables
    - var usePlaceholder = true // Example based on "placeholder" config in pit.json
    - var useInlineCSS = true // Based on "inline-css" config in pit.json
    - var useDataSrc = true // Reflects "data-src" config in pit.json
    - var useDataSrcset = true // Reflects "data-srcset" config in pit.json
    - var allSizes = []

    if usePlaceholder && useInlineCSS
        style.
            .placeholder { background-color: #ececec; } // Placeholder style, adjust as needed

    picture
        if useDataSrcset
            //- Source elements with data-srcset for lazy loading
            source(data-srcset="img/landscape/landscape-100.webp 100w, img/landscape/landscape-300.webp 300w, img/landscape/landscape-600.webp 600w", type="image/webp")
            source(data-srcset="img/landscape/landscape-100.avif 100w, img/landscape/landscape-300.avif 300w, img/landscape/landscape-600.avif 600w", type="image/avif")
        else
            //- Standard source elements without lazy loading
            source(srcset="img/landscape/landscape-100.webp 100w, img/landscape/landscape-300.webp 300w, img/landscape/landscape-600.webp 600w", type="image/webp")
            source(srcset="img/landscape/landscape-100.avif 100w, img/landscape/landscape-300.avif 300w, img/landscape/landscape-600.avif 600w", type="image/avif")

        //- Fallback img element, considering lazy loading
        if useDataSrc
            img(data-src="img/landscape/landscape-100.jpg", alt=altText)
        else
            img(src="img/landscape/landscape-100.jpg", alt=altText)
```

## How to use the Output?

### 1. Include the Pug Mixin
Include the Pug Mixin in one of your Pug files, for example in the home.pug file for your home page.

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
Use JavaScript to activate the image sources for the browser at the right moment:
```js
// Function to change data-src to src
function activateImgSrc() {
    // Activate the src attribute for img elements
    const images = document.querySelectorAll('img[data-src]');
    images.forEach((img) => {
        img.setAttribute('src', img.getAttribute('data-src'));
        img.removeAttribute('data-src');
    });
    
    // Activate the src attribute for source elements within picture
    const sources = document.querySelectorAll('source[data-src]');
    sources.forEach((source) => {
        source.setAttribute('src', source.getAttribute('data-src'));
        source.removeAttribute('data-src');
    });
}

// Function to change data-srcset to srcset
function activateImgSrcset() {
    // Activate the srcset attribute for img elements
    const images = document.querySelectorAll('img[data-srcset]');
    images.forEach((img) => {
        img.setAttribute('srcset', img.getAttribute('data-srcset'));
        img.removeAttribute('data-srcset');
    });
    
    // Activate the srcset attribute for source elements within picture
    const sources = document.querySelectorAll('source[data-srcset]');
    sources.forEach((source) => {
        source.setAttribute('srcset', source.getAttribute('data-srcset'));
        source.removeAttribute('data-srcset');
    });
}

// Call the functions when the entire page is loaded, or whenever
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

### Instructions for the Code Generator
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
|-- source/  
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