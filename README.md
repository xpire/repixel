# repixel
## Convert a scaled jpeg of pixel art into pixel perfect bitmap

# what is repixel?

Given an image of scaled up pixel art which has been affected by:

* jpeg compression resulting in artifacts
* aliasing due to imperfect scaling
* edges being between pixels resulting in fringing and low quality visuals

`repixel` aims to recover the original pixels of the source image. It does this through a few major steps:

1. Use sobel filters to highlight edges. 
    1. The absolute value is taken, and a mean is taken to aggregate all the edges on the page.
    2. peaks are found in the resulting signal, subject to a user tuned threshold parameter
2. the original pixel grid is computed via 1D grid search
    1. the original pixel size is estimated manually as a range between `[min_size, max_size]`
    2. this gives us a range of possible grids to search through between highlighted edges
    3. Hungarian algorithm using MSE cost is used to find the most cost effective matching between peaks and grid lines
    4. the grid that results in the lowest MSE is returned
    5. the grid is extrapolated to the edges of the image
3. for each square in the grid, the colour of the grid is estimated using the median of all colour tuples in that grid

Here is a visualisation of the process taken:

Source image             |  Image with Grid           | Result
:-------------------------:|:-------------------------:|:----:
![](./static/source.png)  |  ![](./static/grid.png)| ![](./static/result.png)

# Usage

1. Put the image you want to rescale to original resolution in the `./src` directory.
2. Go to the `./scripts/recover_pixels.ipynb` and ensure your file is read in the second code block
3. change the `threshold` variable for `sobel_edges` function until only true edges are found (see the example image or the example in this readme for what the gridlines should look like)
4. the last code block should generate the image at the original pixel art's resolution.

# Goals

I have completed the first 2 main goals. However, the colouring of the pixels has been troubling me, and I haven't found a clean method for extracting the original colour of the pixels at the calculated grids.

- [x] Find edges between pixels in image
- [x] Create an equally spaced grid from the edges found
- [x] Estimate the colour of each grid pixel from the source scaled image
    - [x] use median of source pixels in grid cell
    - [ ] use clustering to group similar colours together for a more consistent view
    - [ ] use location based clustering when colouring pixels to ensure local colour consistency
