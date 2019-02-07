# PicoCTF_2017: Meta Find Me

**Category:** Forensics
**Points:** 70
**Description:**

>Find the location of the flag in the image: [image.jpg](image.jpg). Note: Latitude and longitude values are in degrees with no degree symbols,/direction letters, minutes, seconds, or periods. They should only be digits. The flag is not just a set of coordinates - if you think that, keep looking!

**Hint:**

>How can images store location data? Perhaps search for GPS info on photos.

## Write-up

To get all the details of an image, install yum install ImageMagick.
Same can be done by installing **exiftool**.

Now, run `identify -verbose image.jpg` to get all the details of an image.
```
Image: image.jpg
  Format: JPEG (Joint Photographic Experts Group JFIF format)
  Class: DirectClass
  Geometry: 500x500+0+0
  Resolution: 72x72
  Print size: 6.94444x6.94444
  Units: PixelsPerInch
  Type: TrueColor
  Endianess: Undefined
  Colorspace: sRGB
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
  Channel statistics:
    Red:
      min: 0 (0)
      max: 255 (1)
      mean: 234.521 (0.919689)
      standard deviation: 40.0639 (0.157113)
      kurtosis: 27.7297
      skewness: -5.39654
    Green:
      min: 0 (0)
      max: 255 (1)
      mean: 239.257 (0.938264)
      standard deviation: 41.0309 (0.160906)
      kurtosis: 27.718
      skewness: -5.39578
    Blue:
      min: 0 (0)
      max: 193 (0.756863)
      mean: 142.497 (0.558812)
      standard deviation: 24.3041 (0.0953101)
      kurtosis: 27.4201
      skewness: -5.36274
  Image statistics:
    Overall:
      min: 0 (0)
      max: 255 (1)
      mean: 205.425 (0.805588)
      standard deviation: 35.9599 (0.141019)
      kurtosis: 28.2685
      skewness: -5.6137
  Rendering intent: Perceptual
  Gamma: 0.454545
  Chromaticity:
    red primary: (0.64,0.33)
    green primary: (0.3,0.6)
    blue primary: (0.15,0.06)
    white point: (0.3127,0.329)
  Interlace: JPEG
  Background color: white
  Border color: srgb(223,223,223)
  Matte color: grey74
  Transparent color: black
  Compose: Over
  Page geometry: 500x500+0+0
  Dispose: Undefined
  Iterations: 0
  Compression: JPEG
  Quality: 93
  Orientation: TopLeft
  Properties:
    comment: "Your flag is flag_2_meta_4_me_<lat>_<lon>_06c3. Now find the GPS coordinates of this image! (Degrees only please)"
    date:create: 2019-02-05T11:11:53+00:00
    date:modify: 2017-03-31T23:11:10+00:00
    exif:ColorSpace: 1
    exif:Compression: 6
    exif:DateTime: 2016:02:10 11:55:56
    exif:ExifImageLength: 500
    exif:ExifImageWidth: 500
    exif:ExifOffset: 176
    exif:GPSInfo: 218
    exif:GPSLatitude: 88/1, 0/1, 0/1
    exif:GPSLongitude: 4/1, 0/1, 0/1
    exif:GPSVersionID: 2, 3, 0, 0
    exif:JPEGInterchangeFormat: 402
    exif:JPEGInterchangeFormatLength: 6989
    exif:Orientation: 1
    exif:ResolutionUnit: 2
    exif:Software: Adobe Photoshop CS6 (Windows)
    exif:XResolution: 720000/10000
    exif:YResolution: 720000/10000
    jpeg:colorspace: 2
    jpeg:sampling-factor: 1x1,1x1,1x1
    signature: b37c84adee07ac056749f93b85ddab2707762ff8e32a5d6680da63e2e361a8a8
  Profiles:
    Profile-8bim: 9330 bytes
    Profile-exif: 7398 bytes
    Profile-icc: 3144 bytes
      Description: sRGB IEC61966-2.1
      Manufacturer: IEC http://www.iec.ch
      Model: IEC 61966-2.1 Default RGB colour space - sRGB
      Copyright: Copyright (c) 1998 Hewlett-Packard Company
    Profile-xmp: 3511 bytes
  Artifacts:
    filename: image.jpg
    verbose: true
  Tainted: False
  Filesize: 44.3KB
  Number pixels: 250K
  Pixels per second: 0B
  User time:Looking onto the GPS section of the image's metadata reveals

    Latitude: 7° 0’ 0” N
    Longitude: 96° 0’ 0” E

Without degree symbols, direction letters, minutes, seconds or periods, we get
    
    Latitude: 7
    Longitude: 96

Now, `7, 96` is not the answer, nor is any other combinations. So what are we missing? Let's open the file in hex form and do a search for `flag`.

    00005bb0:  0001 0300 1003 0203 0600 0000 0000 0000 0000 0000 00ff fe00  :........................
    00005bc8:  7522 596f 7572 2066 6c61 6720 6973 2066 6c61 675f 325f 6d65  :u"Your flag is flag_2_me
    00005be0:  7461 5f34 5f6d 655f 3c6c 6174 3e5f 3c6c 6f6e 3e5f 3163 3166  :ta_4_me_<lat>_<lon>_1c1f
    00005bf8:  2e20 4e6f 7720 6669 6e64 2074 6865 2047 5053 2063 6f6f 7264  :. Now find the GPS coord
    0.000u
  Elapsed time: 0:01.000
  Version: ImageMagick 6.7.8-9 2016-06-22 Q16 http://www.imagemagick.org
  ```
  **Properties** section has all the information we need.
`exif:GPSLatitude: 88/1, 0/1, 0/1`
`exif:GPSLongitude: 4/1, 0/1, 0/1`
`comment: "Your flag is flag_2_meta_4_me_<lat>_<lon>_06c3. Now find the GPS coordinates of this image! (D5c10:  696e 6174 6573 206f 6620 7468 6973 2069 6d61 6765 2120 2844  :inates of this image! (D
    00005c28:  6567 7265 6573 206f 6e6c 7920 706c 6561 7365 2922 ffdb 0084  :egrees only please)"`....

Easily found, we now have the format. Piecing together, we replace `<lat>` and `<lon>` with our previous coordinates.

Therefore, the flag is `flag_2_meta_4_me_88_4_06c37_96_1c1f`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc1NzEwMjM4NiwtMTg3Mjk0NTczNSw4MD
kzMTU3OThdfQ==
-->