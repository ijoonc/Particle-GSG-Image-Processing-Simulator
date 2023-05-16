## Image Processing Simulator for Krauss Lab Tribology Research Fall 2022 - Spring 2023. 

#### This repo also contains Dummy GSG Images for training and testing - 0.1%, 1%, 2%, 3%, 4%, and 5% Hitec dispersant additives were suspended with  GSG in the particle images.   



**Particle Image Threshold Simulator**

This Google Colab Notebook provides an efficient, alternative method to threshold images instead of using ImageJ. After grayscale, ImageJ requires 4 manual steps for threshold:

1) *Plane Brightness Adjustment:* used to shade background evenly for each image to avoid illumination errors.
2) *Threshold:* helps the system identify the particle clumps in the image.
3) *Watershed:* breaks up the particle clumps that are too close to one another.
4) *Analyze Nuclei:* obtain numerical info about the particles such as particle count, total area of clumps, and average size of clumps.

Note: We must also remove all contaminants in the images prior to analysis. ImageJ (and the Threshold Simulator) cannot accurately differentiate between contaminants and the particles of interest (yet). Using ImageJ also leaves some particles unaccounted for when obtaining numerical data, especially clumps around the edges of the image.

Our *automated Threshold Simulator* accounts for these unaccounted particles and has 5 main steps after grayscale:

1) *Otsu's Binary Threshold:* a popular Python thresholding algorithm that provides a threshold effect on particle images.
2) *morphologyEx()*: a Python function that removes noise in the particle image.
3) *Dilation:* helps to make particles clearer and easier to visualize; this filter is applied evenly across the image, but its effect is greatest on particles that seem partially faded or blurry.
4) *Distance-Transform:* a Python function that helps to improve the accuracy of detected particles, especially those that are farther away from the center of the image.
5) *Resize and Final Adjustments:* generate a black background (since the background of the processed image is also black), shrink the processed image without losing resolution, and place resized image onto black background. In effect, this makes it seems as though the particles along the edges of the image are now placed more towards the center.

Source: https://www.bogotobogo.com/python/OpenCV_Python/python_opencv3_Image_Watershed_Algorithm_Marker_Based_Segmentation.php



**Machine Learning Algorithm**

We face a huge barrier where our threshold simulator only applies to images that have been shaded evenly prior to analysis. Providing images with uneven shading to this program will cause it to produce very inaccurate results. Precisely, our algorithm cannot tell the difference between the particles of interest and the darker shading in the image. This causes Otsu's Binary Threshold to fail, leading to the following sequences to also fail and produce inaccurate results.

We have two solutions that are work in progress.

1) *Rolling Ball*: a Python library specifically designed to help reduce shading errors in images. It is the same library that ImageJ uses to apply shading corrections. 

> How it works: separated background from objects of interest, then evenly shades the background, and places objects (the particles) on top of the new background.

> Issue: this function does not account for ALL shading differences. Our threshold simulator takes into account the specific pixel values of the background. Even after the rolling ball correction, the pixel values still heavily contrast with one another, causing a black and white "noise-like" image.


2) *CLAHE:* stands for Contrast Limited Adaptive Histogram Equalization; enhances the contrast of the image. 

> How it works: divides the image into small tiles and equalizes each tile's shading histogram. This helps to improve local contrast while reducing over-amplification of noise.

> Issue: The pixel values of the background image STILL heavily contrast from one another even with contrast enhancement and de-amplification of noise.



**What To Do Moving Forward**

We plan on utilizing the BaSiCs (Background and Signal Correction) Python Library. This is a software tool for background and shading corrections of optical microscopy images. We can even correct uneven background illumination, autofluorescence, and other sources of background signals. We believe this may contain the solution we have been looking for in terms of shading correction!

Source: Peng, Tingying, et al. “A Basic Tool for Background and Shading Correction of Optical Microscopy Images.” Nature News, Nature Publishing Group, 8 June 2017, https://www.nature.com/articles/ncomms14836/. 

*Some Barriers to Note:* 

1) We are dealing with complex image structures so it is difficult to accurately detect and segment particle clumps as with any other Image Processing library.
2) BaSiCs functions require parameter tuning and these measures are typically unknown for our images.
3) BaSiCs functions also require preprocessing of images such as denoising, smooting, and image enhancement. Our current algorithm preprocesses images AFTER the shading correction, not prior.

We also plan to develop an *automated Watershed algorithm*. Samples with a large amount of suspeneded GSG tend to be very dense so particle clumps are usually connected, making it difficult to differentiate using AI and the human eye. We can automate the Watershed process by implmenting an algorithm that can account for ImageJ particle count errors AND differentiate the clumps accordingly.

