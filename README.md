# LeavesDataset

## INTRODUCTION

Automated leaf segmentation is basic for many agriculture-related projects like disease detection from plant leaf, leaf classification or identification etc. But at the same time, it is an extremely difficult part. Due to variability in colour, shape and size of leaves, it becomes extremely difficult to have a general method for all species of plants. So here we have focused on only one species. The aim of this report is to get leaves from an image as precise as possible rather than getting all leaves present in the image. Special care has been taken to identify background as precisely as possible. Here part of the image containing plant is known as foreground while the remaining part is known as background. In this report, we have discussed leaf segmentation for specific plant i.e. brinjal plant.

## DATA

We have used images of eggplants captured from 13MP
smartphone camera with 1.34-micron pixel size and an f/2.2
aperture. All the images show the top view of plants with 3120
x 4160 pixels resolution. Images vary from young to old plants
within a duration of two months. Images are also taken during
a different time of day. Some images are taken in the morning,
some during noon and some in the evening. Some images are
taken before watering of plant and some after watering. In this
data, we have tried to cover all different combination possible
which effect size, shape and colour of the plant. Total of 100
images of eggplants is used to extract leaves from each image.
Each eggplant image contains on an average 15 leaves.

## METHODOLOGY AND APPROACH
Here first two steps extract the foreground from an image.
As shown in figure 1 in the first step we are forming a
cluster of pixels which have the same characteristic using
superpixel clustering [1]. For superpixel segmentation, we are
using simple linear iterative clustering (SLIC) algorithm.

As eggplant are green in colour, we are extracting superpix-
els with green mean colour from the image. So these extracted

superpixels collectively form foreground. If the background is
grassy surface then it can be wrongly identified as a foreground
but this problem will be taken care in the fourth step.
After extracting foreground edge is detected by using canny
edge detection so we can separate each leaf. Now to find
seed for each leaf we select such pixel which is present in
foreground and it is not an edge. After finding seed by simple
region growing approach mask is formed. If the mask contains
some hole then it is corrected by filling that hole. This can
be due to some disease or dryness of leaf which is not green
in colour. If the size of the mask is greater than a particular
threshold then all the superpixels which cover this mask are
selected as leaf image. This threshold is useful to identify the
difference between grass and leaves. As the mask of grass will
be much smaller in size than that of leaves.

[[Add Image Here]]

#### Simple linear iterative clustering (SLIC)

Superpixels are a group of pixels with the same character-
istics. Superpixel segmentation is local clustering of pixels in

5D space (labxy). Where xy is the position of pixels while the
lab is a vector in LAB colour space. In LAB colour space
l component represent lightness of pixel from 0 to 100. a
component represents green to red colour while b component
represents blue to yellow. a and b component value varies
from -128 to 128. As we are taking lab value in consideration
it can easily differentiate between foreground and background.
It also differentiates between different leaves due to high lab
value of edges.
A number of segments is the only parameter in the SLIC
algorithm. Now suppose if there are N pixels in a given image
and we want M superpixels. As superpixels are almost equal
in size due to its confined search area. So each segment will
have approximately N

M pixels. Hence each cluster center will

be R pixels apart as shown in figure 2.
R =
r
N
M

(1)

Here N represent number of pixels in image,
M represent required number of superpixels
As shown in figure 3 search area of local clustering for
a particular centre is 4R2 pixels surrounding that centre. So
pixels from only this search area can be assigned to that
particular centre.
In Euclidean distance, position distance can reduce the
effect of lab vector distance. So SLIC algorithm instead of
using normal distance measure it uses normalized distance
measure.

D =
s
Dxy
2
Pmax
2 +
Dlab
2
Lmax
2

(2)
Here Dxy represent euclidean position distance between
pixel and centre while Dlab represent euclidean lab vector
distance between pixel and centre. Pmax represent maximum

distance between pixel and center and Lmax represent maxi-
mum lab vector distance between center and pixel.

Now Pmax = R as maximum position distance in this
search area is R and Lmax vary depending on the image
so will replace it with constant L. The value of L mostly
varies from zero to forty. In this report, we have used 25 for
a value of L. Value of L represents the effect of lab vector
distance in forming superpixels. By increasing value of L can
increase effect of lab vector distance and by decreasing value
of L can increase effect of pixel position distance in forming
superpixels.

D =
s
Dxy
2
R2
+
Dlab
2
L2

(3)
In SLIC algorithm first cluster centres are assigned to each
cluster as shown in figure 2 then each pixel is assigned to its
cluster depending on the value of D with respect to the cluster centre. If pixel fall in more than one search area of centres
then centre which has a minimum value of D with that pixel
is assigned to that pixel.
After assigning each pixel to its centres, centres are shifted
to the pixel which has an almost average value of the labxy
vector. Here error is defined which is an average of the distance
between previous centres and new centres. This process is
iterated till error is less than the defined threshold.
Figure 4, 5 and 6 shows the superpixels after 5, 7 and 10
iterations. We have observed for this data that for most of
images iteration required is between 10 to 15.
The time complexity of this algorithm is O(NI), where I
represent a number of iteration, but as I is always less than
15-time complexity of this algorithm is O(N).

#### Extract foreground
Eggplants are green in colour, so to extract foreground all
superpixels with green mean colour are extracted. In LAB
colour space value less than -5 of a coordinate defines the
green colour. All the segments with green mean colour are
extracted. As discussed above background with grass can be
identified as foreground. This problem is taken care of by
extracting leaves with mask size more than a specific value.
Some plant might have green colour value more than -5 which
can be identified as background. But our main focus is to not
identify background as foreground. Figure 7 shows extracted
foreground for particular image.

#### Edge detection
After extracting foreground edge of leaves are detected
using canny edge detection. Canny edge detection removes
noise using the Gaussian filter. It is immune to a noisy
environment. It is a well-defined method and offers reliable
detection. Figure 8 shows detected edges on foreground figure

#### Leaf mask
Simple region growing approach is used to get a leaf mask.
The seed for region growing is selected by simple iteration
with condition that if the pixel is in the foreground and not an
edge. This leaf mask is then corrected if there are any holes
in it due to disease or dryness. Figure 9 shows a leaf mask
before correction.

#### Leaf extraction
At last all superpixels which fall in the region of this leaf
mask are extracted. These superpixels collectively form a leaf.
Figure 10 shows extracted leaf of leaf mask shown in figure
9

## RESULT AND CONCLUSION
Results of this method depend on different parameters like
the number of segments, Gaussian filter, a component values
for the green colour, size of mask allowed etc.
A number of segments depend on the size of the image
as well as the distance between the camera and plant. If a
number of segments are more than required than most of the
superpixels will be of no use as it will form a cluster which is
squared in shape. If most of the clusters are squared then there
will be no much difference between pixels and superpixels. If
the number of segments is less than required than some pixels
can fall in more than one leaves which will again hinder the
purpose of superpixels. In this method, we have taken the
value of a number of segments equal to 800 by considering
the above observations.
As for the value of a component and size of the mask,
our only concern is background should not be identified as
foreground it becomes easy to find the required value. Size
of the mask can be useful to detect the difference between
leaves and grass. In this method, if mask size is greater than
4 superpixels then it is considered to be a leaf.
In this method, we have seen that leaves identification are
more accurate in older plants than younger plants due to its
texture. The texture becomes lighter as the plant grows older
which help in detecting edges with less spurious edges or weak
edges. As the plant grows older midrib and veins become less
visible in this plant. Weak edges are formed due to veins and
midrib.
Figure 12 shows leaves extracted from figure 11. The image
used in the example is of the younger plant. Leaves other than
shown in figure 12 are either not detected due to the size of
the mask or due to spurious edges generated by its veins. As
our main focus was to not detect background as foreground
which can be seen from figure 12 is easily satisfied.
We have created a dataset of eggplants with more than 100
images and more than 1500 leaves in total. This dataset can
be used in agriculture-related projects like disease detection,
leaf classification, leaf identification etc.