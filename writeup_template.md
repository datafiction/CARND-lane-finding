#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of following steps. 

#### Step-I:

First, I converted the images to grayscale, using  ```grayscale``` helper function. The output image is green because it choose single color. In order to get it as grey we must set it as ```plt.imshow(gray_image, cmap='gray')```.

```
grey_image = grayscale(image)

```


<img src="https://github.com/Vasuji/carnd-project1/blob/master/pipeline_images/1grey_image.jpg?raw=true" width="350" height="200" />


#### Step-II:

In second Step, I added the property of gaussian blur to the image. Eventhough it was optional step because ```Canny``` function is going to use ```5 x 5``` kernel size for adding gaussian noise, I added this to see it's effect.

```
gaussian_blur_image = gaussian_blur(grey_image, kernel_size=3)

```
<img src="https://github.com/Vasuji/carnd-project1/blob/master/pipeline_images/2gaussian_blur_image.jpg?raw=true" width="350" height="200" />



#### Step-III:

In third step, I applied canny helper function to find sharp edges. Sharp edges are because of change in pixcel values which we call gradient.
```
 canny_image = canny(gaussian_blur_image,\
                    low_threshold=50,\
                    high_threshold=150)
 ```
 <img src="https://github.com/Vasuji/carnd-project1/blob/master/pipeline_images/3canny_image.jpg?raw=true" width="350" height="200" />


#### Step-IV:

In fourth step, I selected the region of interest. For this we nne to set vertices as :
```
vertices0 = np.array([[(0,imshape[0]),\
                      (450, 320),\
                      (500, 320),\
                      (imshape[1],imshape[0])]], dtype=np.int32)
```
Using these verticies we can get masked image by using ```region_of_interest``` helper function.

```
 masked_image = region_of_interest(canny_image,\
                                  vertices=vertices0 )
```


 <img src="https://github.com/Vasuji/carnd-project1/blob/master/pipeline_images/4masked_image.jpg?raw=true" width="350" height="200" />
 
#### Step-V:

In step V, I used ```houg_line``` helper function. The most important code in this section is ```draw_lines``` function

```
 line_image = hough_lines(masked_image, rho =2, \
                          theta=np.pi/180,\
                          threshold=20, \
                          min_line_len=25,\
                          max_line_gap =10)
```

Here are few sub-steps for ```draw_line``` helper function

  * Set minimum slope threshold and avoid infinite slope in maximum slope threshold
  
  * Seperate allowed lines detected by function ```cv2.HoughLinesP``` into two families right_lines and left_lines
  
  * Use linear regression function ```interpolation``` to ge 'm' and 'b' value for a line'y=mx+b' for each line(right and left)
  * Use the value of 'm' and 'b' to draw lines finding end point (use```end_pint_finder```) of line in the image.
  


 <img src="https://github.com/Vasuji/carnd-project1/blob/master/pipeline_images/5line_image.jpg?raw=true" width="350" height="200" />
 
#### Step-VI:

In step IV, I used ```weighted_image``` which blends line image over intial image with given parameters ```?? , ?? and ??```

```

weighted_image = weighted_img(line_image, initial_img,
                           ??=0.8,\
                           ??=1.,\
                           ??=0.)
```
<img src="https://github.com/Vasuji/carnd-project1/blob/master/pipeline_images/6weighted_image.jpg?raw=true" width="350" height="200" />



###2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when it is  night and dark, rain, snow etc which makes lane sign unclear. 

Another shortcoming could be considering rode lane as perfect straight lines. If speed of car is very high and rode is highly curved or if roadlanes appear curved line due to high temperature.


###3. Suggest possible improvements to your pipeline

A possible improvement would be to train the car in every such situations like rainy day, snow , night etc.

Another potential improvement could be to use other radiation camera for example infrared camera at night. What about using sound waves like bats use it to detect the position of wall or obstacle?
