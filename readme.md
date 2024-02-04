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