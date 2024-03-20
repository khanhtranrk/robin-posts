# How to Create a Simple PNG File

## Issue
Draw a yellow circle on a red background.

## Dimensions
Create a 16x16 pixel image from scratch.

## PNG Data Representation

### Color Values
Colors can be represented using grayscale or RGB (red, green, blue) sample data. Learn more about this here.

### Image Layout
A PNG image is an array of rectangular pixels. Each pixel’s size is determined by its bit depth. The bit depth specifies the number of bits used for each sample. Learn more about this here.

### File Structure
A PNG file always starts with a signature followed by chunks. Each chunk contains four parts:
1. Length (4 bytes): Length of the chunk data (excluding chunk type, CRC, and itself).
2. Chunk Type (4 bytes): Types include IHDR, IDAT, and IEND.
3. Chunk Data: Data specific to the chunk type.
4. CRC (4 bytes): Calculated using the CRC algorithm.

## Creating a 16x16.png
### IHDR Chunk
The IHDR chunk contains basic image information. Our data includes:
* Width: 16
* Height: 16
* Bit depth: 8
* Color type: 2 (for color images)
* Compression method: 0
* Filter method: 0
* Interlace method: 0

The complete IHDR data:

```
0000 000D 4948 4452 0000 0010 0000 0010 0802 0000 0090 9168 36
```

### IDAT Chunk
The IDAT chunk contains actual image data. We’ll create a square array with red and yellow pixels. After preprocessing and compression, the data becomes:

```
0000 0032 4944 4154 789c 63f8 cfc0 4012 224d f500 6bf8 4fbc 86ff a888 8086 ffd8 10f5 3460 558d aa87 de4e 2227 94c8 8907 7a25 0d1c 0800 5d05 2fdf d75a 7c32
```

### IEND Chunk
The IEND chunk signals the end of the PNG file. It has no data:

```
0000 0000 4945 4E44 AE42 6082
```

Combine all chunks to create the complete PNG file.

```
8950 4E47 0D0A 1A0A 0000 000D 4948 4452 0000 0010 0000 0010 0802 0000 0090 9168 36 0000 0032 4944 4154 789c 63f8 cfc0 4012 224d f500 6bf8 4fbc 86ff a888 8086 ffd8 10f5 3460 558d aa87 de4e 2227 94c8 8907 7a25 0d1c 0800 5d05 2fdf d75a 7c32 0000 0000 4945 4E44 AE42 6082
```

## References
1. http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf
2. https://en.wikipedia.org/wiki/PNG
3. https://www.zlib.net/feldspar.html
4. https://en.wikipedia.org/wiki/Cyclic_redundancy_check
