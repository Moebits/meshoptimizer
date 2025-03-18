# 🐇 meshoptimizer (vrm support)

## Purpose

This is a workaround to add support for vrm in gltfpack. I also compiled gltfpack to WASM to use
it in the browser.

## Compiling
```c
git clone -b gltfpack https://github.com/zeux/basis_universal
mkdir build
cd build
cmake .. -DMESHOPT_BUILD_GLTFPACK=ON -DMESHOPT_BASISU_PATH=basis_universal -DCMAKE_BUILD_TYPE=Release
cmake --build . --target gltfpack --config Release
```

## Compiling WASM
```c
emcmake cmake .. -DMESHOPT_BUILD_GLTFPACK=ON -DMESHOPT_BASISU_PATH=basis_universal -DCMAKE_BUILD_TYPE=Release
cmake --build . --target gltfpack --config Release
```

## Basic Usage
```c
./gltfpack -i input.vrm -c -si 0.7 -o output.vrm
```

## WASM Usage
```c
window.GLTFPack.FS.writeFile("input.vrm", new Uint8Array(vrmBuffer))

let args = ["gltfpack", "-i", "input.vrm", "-c", "-si", "0.7", "-o", "output.vrm"]

const argsPtr = window.GLTFPack._malloc(args.length * 4)

for (let i = 0; i < args.length; i++) {
    const strPtr = window.GLTFPack._malloc(args[i].length + 1)

    window.GLTFPack.stringToUTF8(args[i], strPtr, args[i].length + 1)
    window.GLTFPack.setValue(argsPtr + i * 4, strPtr, "i32")
}

window.GLTFPack.ccall("webgltfpack", null, ["number", "number"], [args.length, argsPtr], null)

const outputArray = window.GLTFPack.FS.readFile("output.vrm") as Uint8Array
```