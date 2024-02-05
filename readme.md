# Textures

## Types of Textures

- Color (Albedo) - applied on geometry
- Alpha Texture - gray scale image
- Height (displacement) - gray scale image that moves vertices to create some relief on a plane needs subdivision
- Normal - adds details to light, doesn't need subdivision. The vertices wont move.
- Ambient Occlusion - grey scale image and adds fake shadows in crevices. Not accurate.
- Metalness - Grey scale image, white is metallic, black is non-metallic. Mostly used for reflections.
- Roughness - Greycale image, white is rough, black is smooth. Mostly for light dissipation. 

## PBR Principles (Physically Based Rendering)

The goal is to get a realistic result by using real life like calculations.

Articles:

[Basic Theory of PBR](https://marmoset.co/posts/basic-theory-of-physically-based-rendering/)
[PBR and You can Too!](https://marmoset.co/posts/physically-based-rendering-and-you-can-too/)

## How to load textures (Vanilla JS)

The folder structure in  vite is:

root
├── src
│   ├── index.html
│   ├── styles.css
│   ├── script.js
├── static
│   ├── textures
│       ├── door

```javascript
import imageSource from '../static/textures/door/color.jpg'

/**
 * Textures
 */

//Step 1: Import Image and Create Texture

const image = new Image()
const texture = new THREE.Texture(image)
texture.colorSpace = THREE.SRGBColorSpace // Makes it sRGB

image.onload = () => {
    texture.needsUpdate(true)
}

image.src = imageSource

// Step 2: Load Texture

const material = new THREE.MeshBasicMaterial({ map: texture })

```

## How to Load Textures (Texture Loader)

```javascript


/**
 * Textures
 */

//Step 1: Import Image and Create Texture (one texture loader can load multiple textures)

const textureLoader = new THREE.TextureLoader()
const texture = textureLoader.load(imageSource)
texture.colorSpace = THREE.SRGBColorSpace

// Step 2: Load Texture

const material = new THREE.MeshBasicMaterial({ map: texture })
```

The texture loader contains callback functions for debugging

```javascript
const texture = textureLoader.load(
    imageSource,
    () => {
        console.log("LOAD")
    },
    () => {
        console.log("PROGRESS")
    },
    () => {
        console.log("ERROR")
    })

```

## Creating a Loading Manager

```javascript
const loadingManager = new THREE.LoadingManager()

loadingManager.onStart = () => {
    console.log("onStart")
}

loadingManager.onProgress = () => {
    console.log("onProgress")
}

loadingManager.onError = () => {
    console.log("onError")
}

// Pass it into texture loader

const textureLoader = new THREE.TextureLoader(loadingManager)

```

## UV Unwrapping

When applying a texture to your geometry. You will notice that a texture will look different over different geometries.

These UV coordinates are predefined. Remember UV Coordinates are coordinates in a 2D UV Map that is meant to represent a 3D Object.

You can access these coordinates from the attributes:

```javascript
const coordinates = geometry.attributes.uv

//Log Result: Float32BufferAttribute

{
    "itemSize": 2,
    "type": "Float32Array",
    "array": [
        0, 1,
        1, 1,
        0, 0,
        1, 0,
        0, 1,
        1, 1,
        0, 0,
        1, 0,
        0, 1,
        1, 1,
        0, 0,
        1, 0,
        0, 1,
        1, 1,
        0, 0,
        1, 0,
        0, 1,
        1, 1,
        0, 0,
        1, 0,
        0, 1,
        1, 1,
        0, 0,
        1, 0
    ],
    "normalized": false
}
```

## Transform the Texture

```javascript

const colorTexture = textureLoader.load(imageSource)

//Always required for color compatibility

colorTexture.colorSpace = THREE.SRGBColorSpace

//Optional Settings

colorTexture.repeat.x = 2
colorTexture.repeat.y = 3

colorTexture.wrapS = THREE.MirroredRepeatWrapping 
colorTexture.wrapT = THREE.RepeatWrapping

colorTexture.offset.x = 0.5
colorTexture.offset.y = 0.5

//Center PivotPoint and Rotate
colorTexture.center.x = 0.5
colorTexture.center.y = 0.5
colorTexture.rotation = Math.PI / 4 //In Radians: 1/8th rotation since PI is half rotation

```

## Filtering and Mip Mapping

Mip Mapping continuously creates smaller versions of our textures until we achieve the smallest 1x1.

### Minification Filter

We use the smaller version of our texture as we move away from the geometry. Although this is done automatically by the GPU. You can modify it.

```javascript

colorTexture.minFilter = THREE.NearestFilter //Super Sharp

```

### Magnification Filter

We can use a larger version of our texture as we get closer if the image is low resolution.

```javascript

colorTexture.magFilter = THREE.LinearFilter //Default

colorTexture.magFilter = THREE.NearestFilter //Super Sharp

```

### Extra:

When exclusively using Nearest Filter on the MinFilter. You don't need to generate Mip Maps for the texture since we will always use the largest.

```javascript

color.texture.generateMipmaps = false
colorTexture.minFilter = THREE.NearestFilter //Super Sharp


```

## Texture Format and Optimization

.jpg - lossy compression but usually lighter
.png - lossless compression but usually heavier

## Sources

[Polygon](https://www.poliigon.com/)
[3D Textures](https://3dtextures.me/)
[ArroWay](https://www.arroway-textures.ch/)
[Substance 3D](https://www.adobe.com/products/substance3d-designer.html)
