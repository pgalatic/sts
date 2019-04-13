# Image Segmentation and Stylization
## with Reference-Guided Mask Propagation and Fast Artistic Videos
A computer vision project by Paul Galatic.

# Background

This work is a combined implementation of two works by Oh et al. and Ruder et al. It is simply an interesting combined use case for these two ideas. The basic premise is this: "What if we could stylize all but parts of an image?" 

In this project, the foreground is segmented from the background by Reference-Guided Mask Propagation [[oh2018fast](http://openaccess.thecvf.com/content_cvpr_2018/CameraReady/1029.pdf)]. The background is then stylized by Fast Artistic Videos [[Ruder18](https://arxiv.org/abs/1508.06576)]. See [report.pdf](report.pdf) for more details.

# Installation

Some elements of what is required are included in requirements.txt.
```
pip install -r requirements.txt
```
Additional dependencies include:
* RGMP (see [here](https://github.com/seoungwugoh/RGMP))
* Fast Artistic Videos (see [here](https://github.com/manuelruder/fast-artistic-videos))

You should clone those respositories into this one. See their respective READMEs for additional information, or consult the updated READMEs in this repository.

Updated versions of some of the files in those repositories are included in this repository, notably updates to the script 'fast-artistic-videos/stylizeVideo_deepflow.sh' and associated .lua files that allows it to halt and resume computation on a given frame number. Such files have the suffix '_UPDATED' and should replace their un-updated counterparts once installation is otherwise complete. 

Additionally, various files have been updated with better documentation and more readable structures, and some files (such as 'RGMP/watershed_driver') are not present at all in the original repositories and are included for the convenience of the user.

# How to Use

A lot of the following is "easier said than done", but *in theory* the method is relatively simple.
1) Acquire a video. The video should have no jump-cuts and should have as high a frame rate as possible (resolution only matters to the extent that your PC has enough memory to run neural style transfer; my computer struggled with 360p but your mileage may vary, especially if you parallelize). The video should also have a clear "target", i.e. a person running, a car, or some other large, contiguous object that moves through the scene. This target should be visible during the first frame--the video should be clipped to meet this condition.
2) Use RGMP to segment the target from the background of the video and generate a set of masks. See the (README)[RGMP/README.md] for details.
3) Use Fast Artistic Videos to produce a set of stylized frames. See the (README)[fast-artistic-videos/README.md] for details.
4) Split the video into a set of frames. Organize them with the other sets of frames into a folder like so:
```
# let's say my video is named 'shark.mp4'
shark/
    original/   # the frames of the original video
        00000.png
        00001.png
        ...
    mask/       # the masks generated by RGMP
        00000.png
        ...
    style/      # the frames of the stylized video produced by fast-artistic-videos
        00000.png
        ...
```
5) Run the following command to assemble these three sets of frames into a full-fledged video (with FFMPEG!):
```
./assemble.sh <your-folder-name>
```
In keeping with the example above:
```
./assemble.sh shark
```
This command will produce two videos at the top level of the 'shark' folder; one with audio, the other without.

## Known issues

* For some reason, the ffmpeg commands in 'assemble.sh' create videos that cannot be played in Chrome, though they play on Windows Media Player just fine. 

## References

```
@InProceedings{oh2018fast,
author = {Oh, Seoung Wug and Lee, Joon-Young and Sunkavalli, Kalyan and Kim, Seon Joo},
title = {Fast Video Object Segmentation by Reference-Guided Mask Propagation},
booktitle = {The IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
year = {2018}
}

@Article{Ruder18,
author       = "M. Ruder and A. Dosovitskiy and T. Brox",
title        = "Artistic style transfer for videos and spherical images",
journal      = "International Journal of Computer Vision",
number       = "11",
volume       = "126",
pages        = "1199-1219",
month        = "Nov",
year         = "2018",
note         = "online first",
url          = "http://lmb.informatik.uni-freiburg.de/Publications/2018/RDB18"
}
```