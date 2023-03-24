# Image Processing Tools for Whole Slide Imaging Brightfield Histology %/n

<p>% ----------------------------------------------------%<p>
<p>Welcome to my repo! My name is Michael Glendinning, and here you will find a collection of some of the MATLAB code I've written over the course of the past few years to assist me in various image processing tasks encountered as a research scientist studying molecular biology (the past 6 years of my career has been devoted to understanding the neurovasculature, both in development and pathology).<p>

<p>I got very excited at my most recent job when I discovered that our microscopes had the ability to acquire whole-slide images. Generally speaking, its quite easy to lose sight of the forest between the proverbial trees when looking under a microscope, particularly at high magnification. Sometimes, it is also hard to avoid unknowningly introducing bias. I found it to be a substantial improvement in my workflow to capture the whole slide image of my experiments and then use software to randomly sample them to extract regions of interest (ROIs), removing the opportunity for my biases to influencing my choice of ROIs, and reducing the chance that I miss something interesting on the mesoscopic scale. But, I had to adapt some code and write some new code to handle such large images, out of which this respository was born. <p>
  
<p>By virtue of my recent unemployment, I've finally been able to devote some time to organizing and cleaning some of the plentitudes of code I've accumulated that leverage MATLAB to both impel and propel my neuroscience research. <p>

<p> Note, this is a living repository, and so I cannot promise that everything works flawlessly (particularly if your images are substantially different from what I have been using for testing), nor can I promise that the code won't be rewritten if you return back here at some later date! But, I've done what I can to make these scripts maximally robust and adaptable. Feel free to reach out to me or submit a pr in the event something does not make sense, bugs are encountered, or there is room for improvement.  <p>

<p> My inital commit includes two tools. These were initially part of one longer workflow, the rest of which I'll upload in due time. But for now, please enjoy! Below are some 

<p>% ----------------------------------------------------%<p>
  
<p>IMAGE SEGMENTATION<p>
<p>The first, and more thoroughly tested/annotated, is a library of tools for the semi-automated foreground/background segmentation of large, brightfield whole-slide images. As long as your images hold the following attributes, this tool should be well-suited to your workflow:<p>
  - your images are in RGB format. MATLAB has a nice library of color space conversion functions if youre images are described in another color space. If you have only grayscale images though, you will need to rewrite some code or devise a creative work-around. 
  - you images must have a light-colored background with a homogenous texture.
  - your images must have foregrounds of darker-colored blobs, which are surrounded on all sides by background (originally I wrote this with the assumption of the foreground being one contiguous blob, but recently have been trying to expand this range to include multiple blobs.... still a work in progress though. If need be, one can always segment 1 blob at a time, and after  segmenting one, inpaint over it before moving onward to the next.)
  - the images are fairly big and high-resolution (my tiff files that I've been testing with range from 0.5 to 10 gigabytes in size). I've tested as small as 400x400x3 images and had no issue. Smaller than that can lead to some issues. Larger than that will work, but it may go slow.... And, if your images are super blurry or low-resolution, not only will my code not work, but your downstream applications will also likely not work -- image processing should NEVER be used to compensate for crap images; the answer to bad images is not good code, it is better images. 
 
 <p>First and foremost this repository is an exploratory tool. This is because instead of having just 1 segmentation strategy, I've included a menu of 8 different segmentation algorithms you can try out. It also includes 6 different "refinement" algorithms to improve a segmentation. Because consistency and reproducibility are super important in science, and are inherently at odds with this kind of "a la carte" approach, the suggested usage of this tool is to first determine what approach is best-suited to your data. After you've determined that, every image should be segmented the same way. <p>
  
<p>% ----------------------------------------------------%<p>
 
 <p>IMAGE REGISTRATION<p>
 <p>This collection of functions is less modular than the segmentation code. Its goal is the registration of chromogenic stains performed on serial sections and imaged using brightfield microscopy. <p>
  <p> My motivation was a project in which I was interested in characterizing how the expression of certain markers of interest varied in the immediate vicinity of a previously injured area of a tissue, long after the injury was sustained and had healed. In lieu of fancier approaches, for a number of reasons (the autofluorescence of human autopsy tissue, limited access to good tissue, and time) a conservative approach was warranted. This meant single-marker IHC with a hematoxylin nuclear counter-stain in serial sections. As such, after whole slide images were collected, it was absolutely critical we register the staining that delineated where in the tissue the injury had once occurred, vs where was totally normally and always healthy. Once we could overlay this "map", it was trivial to segment the tissue into always healthy and not always healthy and the characterize the expression patterns of our marker.  <p>
  
<p> I struggled to find a single algorithm that could provide me with enough robustness/efficiency to be able to register my entire dataset. Ultimately, I found success implementing a gradual approach. By stringing together different registration techniques, I was able to get even the most stubborn of image pairs to register. It is structured into three parts in its present form:<p>
  - part 1, the coarsest registration, relies only on affine transformations. The coordinates of this affine transformation are calculated using 4 control points, each located in the "corners" of the tissue (my sections teneded to have rectangular proportions, but I've since been able to apply the technique to coronal sections of mouse brain). These points are selected programmatically but a GUI is included to refine their placement. I augment these 4 points further with a 5th point located at the centroid of the foreground. 
  - part 2, estimates a local spatial transformation in order to register the images to one another. To do this effectively, many more control points had to be placed, and there is nothing I hate more than manually placing control points (not to mention that is hardly reproducible and time-intensive). As such, I've done my best to remove all user input to this process. Although it works well, I've added for completeness the option to reposition these points right before the local spatial tranformation is calculated. After the points are set, I have a GUI which presents the results of 3 different local spatial transformations, so the user can chose their favorite. 
   - part 3, calls Thirion's demon algorithm, as implemented in the image processing toolbox built in to MATLAB. Because of the multi-resolution approach of this implementation, this step usually takes at most 2-3 minutes. 
  <p>After all this, I usually achieve >85% correlation between the moving and fixed images. Which, for our needs, was more than sufficient<p>. 
  
<p>% ----------------------------------------------------%<p>

  
