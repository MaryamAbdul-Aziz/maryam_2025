---
layout: post
comments: true
title: Color Codes, Images, Base64 Hacks
type: hacks
courses: { compsci: {week: 31} }
---

## Exercises

### Exercise 1

1. Convert these hex codes to RGB & RGBA (A = 1):
- #1E90FF
    - rgb(30,144,255)
    - rgba(30,144,255, 1)
- #32CD32
    - rgb(50, 205, 50)
    - rgba(50, 205, 50, 1)
- #FFD700
    - rgb(255,215,0)
    - rgba(255,215,0, 1)

2. Write an RGBA for 50%-transparent teal (rgb(0,128,128)).
    - rgba(0, 128, 128, .5)

### Exercise 2

[6820d3] [p2053d3]
[p2053d3] [6820d3]

24 bits per pixel
4 pixels

24 * 4 = 96 bits
96 bits/8 = 12 bytes

### Exercise 3

1. Base64-encode the string: Cat
    - Y2F0
2. Create a 1×1 red PNG, convert it to Base64, and embed in <img> tag.
    - <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVR4nGNgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII=">
