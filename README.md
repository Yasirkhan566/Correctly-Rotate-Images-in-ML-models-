<h1>OpenCV Project for Differentiating Images</h1>
<h2>Introduction</h2>
<p>This project demonstrates how to process an image using OpenCV to detect contours, create a mask, and perform various operations on the Region of Interest (ROI). The script reads an input image, converts it to grayscale, applies adaptive thresholding, finds contours, and then processes the largest contour.</p>
<h2>Prerequisites</h2>
<p>Ensure you have the following packages installed:</p>
<ul>
            <li>numpy</li>
            <li>argparse</li>
            <li>imutils</li>
            <li>opencv-python</li>
</ul>
        <p>You can install these packages using pip:</p>
        <pre><code>pip install numpy argparse imutils opencv-python</code></pre>

        <h2>Usage</h2>
        <p>To run the script, use the following command:</p>
        <pre><code>python your_script.py -i path_to_image</code></pre>

        <h2>Code Explanation</h2>
        <pre><code># import the necessary packages
# Environment: open_cv_env
# Author: Yasir Khan Bahadar
import numpy as np
import argparse
import imutils
import cv2

# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--image", required=True, help="path to the image file")
args = vars(ap.parse_args())

image = cv2.imread(args["image"])
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

thresh = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY_INV, 11, 2)

cnts = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)

# ensure at least one contour was found
if len(cnts) > 0:
    # grab the largest contour, then draw a mask for the pill
    c = max(cnts, key=cv2.contourArea)
    mask = np.zeros(gray.shape, dtype="uint8")
    cv2.drawContours(mask, [c], -1, 255, -1)
    cv2.imshow('mask', mask)
    # compute its bounding box of pill, then extract the ROI,
    # and apply the mask
    (x, y, w, h) = cv2.boundingRect(c)
    imageROI = image[y:y + h, x:x + w]
    maskROI = mask[y:y + h, x:x + w]
    cv2.imshow("Mask", maskROI)
    imageROI = cv2.bitwise_and(imageROI, imageROI, mask=maskROI)
    cv2.imshow("imageROI", imageROI)
    # loop over the rotation angles
    for angle in np.arange(0, 360, 15):
        rotated = imutils.rotate(imageROI, angle)
        cv2.imshow("Rotated (Problematic)", rotated)
        cv2.waitKey(0)
    # loop over the rotation angles again, this time ensure the
    # entire pill is still within the ROI after rotation
    for angle in np.arange(0, 360, 15):
        rotated = imutils.rotate_bound(imageROI, angle)
        cv2.imshow("Rotated (Correct)", rotated)
        cv2.waitKey(0)</code></pre>

        <h3>Imports</h3>
        <ul>
            <li><code>numpy</code>: Used for array operations.</li>
            <li><code>argparse</code>: Parses command-line arguments.</li>
            <li><code>imutils</code>: Contains convenience functions for image processing.</li>
            <li><code>cv2</code>: The OpenCV library for image processing.</li>
        </ul>

        <h3>Argument Parsing</h3>
        <ul>
            <li><code>argparse.ArgumentParser()</code>: Creates an argument parser.</li>
            <li><code>add_argument</code>: Adds an argument for the image file path.</li>
            <li><code>parse_args()</code>: Parses the command-line arguments.</li>
        </ul>

        <h3>Image Reading and Conversion</h3>
        <ul>
            <li><code>cv2.imread()</code>: Reads the image from the specified path.</li>
            <li><code>cv2.cvtColor()</code>: Converts the image from BGR to grayscale.</li>
        </ul>

        <h3>Adaptive Thresholding</h3>
        <ul>
            <li><code>cv2.adaptiveThreshold()</code>: Applies adaptive thresholding to the grayscale image.</li>
            <li><code>gray</code>: Input grayscale image.</li>
            <li><code>255</code>: Maximum value to use with the THRESH_BINARY_INV thresholding type.</li>
            <li><code>cv2.ADAPTIVE_THRESH_MEAN_C</code>: Computes the mean of a block size neighborhood of each pixel.</li>
            <li><code>cv2.THRESH_BINARY_INV</code>: Inverts the binary threshold.</li>
            <li><code>11</code>: Block size for the neighborhood.</li>
            <li><code>2</code>: Constant subtracted from the mean.</li>
        </ul>

        <h3>Finding Contours</h3>
        <ul>
            <li><code>cv2.findContours()</code>: Finds contours in the thresholded image.</li>
            <li><code>thresh.copy()</code>: The source image.</li>
            <li><code>cv2.RETR_EXTERNAL</code>: Retrieves only the extreme outer contours.</li>
            <li><code>cv2.CHAIN_APPROX_SIMPLE</code>: Removes all redundant points and compresses the contour.</li>
        </ul>

        <h3>Grabbing Contours</h3>
        <ul>
            <li><code>imutils.grab_contours()</code>: A convenience function to handle different OpenCV versions' return values for <code>findContours</code>.</li>
        </ul>

        <h3>Drawing the Mask</h3>
        <ul>
            <li><code>max()</code>: Finds the largest contour based on the area.</li>
            <li><code>np.zeros()</code>: Creates a blank mask with the same dimensions as the grayscale image.</li>
            <li><code>cv2.drawContours()</code>: Draws the largest contour on the mask.</li>
            <li><code>mask</code>: The mask image.</li>
            <li><code>[c]</code>: The largest contour.</li>
            <li><code>-1</code>: Draws all contours (only one here).</li>
            <li><code>255</code>: The color of the contour (white).</li>
            <li><code>-1</code>: Fills the contour.</li>
        </ul>

        <h3>Bounding Box and ROI Extraction</h3>
        <ul>
            <li><code>cv2.boundingRect()</code>: Computes the bounding box of the largest contour.</li>
            <li>Extracts the ROI from the image and the mask using the bounding box coordinates.</li>
            <li><code>cv2.bitwise_and()</code>: Applies the mask to the ROI.</li>
        </ul>

        <h3>Rotation</h3>
        <ul>
            <li><code>imutils.rotate()</code>: Rotates the ROI by 15-degree increments.</li>
            <li><code>imutils.rotate_bound()</code>: Rotates the ROI by 15-degree increments, ensuring the entire ROI is within the frame.</li>
        </ul>

        <h2>Conclusion</h2>
        <p>This script demonstrates various image processing techniques using OpenCV and how they can be combined to achieve a specific goal. Each function used has alternatives, and understanding these will help you choose the best approach for your particular use case.</p>

        <p>Feel free to explore the code, experiment with different functions, and adapt it to your needs.</p>
