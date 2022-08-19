# Virtual Paint Project

  ## Introduction

How wonder it will be if we can draw things by just waving our finger in the air! But this has been made possible by combining digital drawing and smart photo recognition techniques resulting in a hands-free digital drawing canvas called Air Canvas.

I used Python, its libraries like Numpy, Collections, and computer vision techniques of OpenCV to draw anything on the canvas by just capturing the motion of a colored marker with a web camera. We made a basic framework of Virtual Paint, which enables the user to draw on their system screen by drawing in the air with a bead (I used blue one) which is tracked by the computer webcam. So, to achieve this objective, Color Detection and Tracking along with the masking techniques are used to keep a track on target bead in real time. 


 ## Algorithm

1. Firtsly, read the frames using a webcam.
2. Convert the captured BGR frames to HSV color space.
3. Create the Paint window & Canvas frame with the respective color palette buttons on it. 
4. Create the trackbar window for adjusting the trackbar values.
5. Create the mask and adjust the trackbar values such that the place we want to track sets as white and the rest place as black.
6. Processing the mask by using a kernel for smoothening and removing noise with morphological operations(Erosion and dilation).
7. Detect the contours and then find the center coordinates of the largest contour and keep storing them in the deques for successive frames.
8. Finally, draw the points stored in deques on the frames and canvas window with their respective colors.

 #### *For the color detection part*,
- I used OpenCV function “createTrackbar()” to create the track bars and track the colors in HSV space. So, we created six different track bars for upper Hue, upper Saturation, upper Value, lower Hue, lower Saturation, and lower Value where 'Hue' represents the color, 'Saturation'- the amount to which that respective color is mixed with white & 'Value'- the amount to which that respective color is mixed with black (Gray level).
I used HSV only instead of RGB because the HSV model describes colors similar to how the human eye perceives color whereas RGB defines color in terms of combination of primary colors. That's why I prefered HSV to RGB.

#### *For the marker tracking part*
- On seeing from a webcam's point of view, we would be basically moving the marker in 2D plane. So I had to store all the coordinates of our bead corresponding to each colour. For that, I used the deque data structure that allows us to push and pop from both ends.
Now along with the bead, there comes some impurities with which we got rid of using dilation, and therefore we defined a kernel that would be used in masking.
To create boxes for different colors in the paint window, cv2.rectangle() and cv2.putText has been used. 


#### *For the drawing on the canvas part* 
- We used cv2.line() function to join between any two points and then we showed the output on the canvas using cv2.imshow() function

#### *Implementation of the algorithm in nutshell*

- For capturing the live frames, cv2.VideoCapture is used and for processing of each frame, we run a while loop which will end when the user will press the key ‘q’ on the keyboard. 

- Now we have a binary image ‘mask’ with regions set as white tracking the marker’s position. I used cv2.findContours() function to find all the contours in the mask image.
- This function doesnot modify the source image, rather it returns a modified image as the first of three return parameters- modified image, the contours and the hierarchy.
- We created a three tuple variable ‘cnts,_ ‘ for storing these three values and in ‘cnts’ variable we have our all contours stored which are just Numpy arrays of (x,y) coordinates of boundary points of the object. 


- Now in the current frame if we have found some contours that means in this current frame we have moved our maker bead and hence we will have some points. Several contours will form and all of them may not of our use. So, to resolve this issue we will take the contour of largest length by sorting the contour array in descending order and taking the first element of it. We stored it in ‘cnt’ variable. Around this largest contour we found the coordinates and radius of circle of least radius which encloses it by using cv2.minEnclosingCircle(). We stored the center coordinates in (x,y) and radius in ‘radius’. Now around this contour we drew the above circle with yellow colour by using  cv2.circle() function.

- Now we have to decide for this region of circle that which point in it to select for reference and also it's color. But one problem here is in this region there are infinitely many points and we can’t decide by checking for all the points so we have to take a reference point according to which our decision should be made. The most logical for picking this point is to pick the centroid of the contours. By generating the moments we found the centroid of the contour and stored the centroid coordinates in the ‘ center’ variable.

- *Case 1*
 
    If the y coordinate of center lies where our boxes (Clear,Blue,Green,Red ,Yellow ) are present. It means one of these buttons is clicked . Further five cases are present , these five cases represent which button is clicked and it can be easily found by checking the x- coordinate of the center.
     If the CLEAR ALL button is pressed we clear all the four deques and set all indexes to 0 indicating that now nothing is present in any of the deques. And also we clear the paint window means we will color all the paint window white.
     
     If the BLUE button is pressed, we will set our color index to 0 so that the future points will be stored in deque of blue.
     
     If the GREEN button is pressed, we will set our color index to 1 so that the future points will be stored in deque of green.
     
     If the RED button is pressed, we will set our color index to 2 so that the future points will be stored in deque of red.
     
     If the YELLOW button is pressed, we will set our color index to 3 so that the future points will be stored in deque of yellow.

- *Case 2*

    If the y coordinate of the center lies in the drawing region . In this case further 4 cases are possible . They are as follows :

     If color index=0, it means we have to use blue color for drawing . So we will append this center point to the blue deque.
     
     If color index=1, it means we have to use green color for drawing . So we will append this center point to the blue deque.
     
     If color index=2, it means we have to use red color for drawing . So we will append this center point to the blue deque.
     
     If color index=3, it means we have to use yellow for drawing . So we will append this center point to the blue deque.

- If the contour array is empty i.e. we donot have any contour, we donot have to do anything but when the bead appears in future frame to another position it does not make a straight line of that color between those two. In this case we will remove the deque of the previous points from each of the four array of deques as all the latest deques must have been processed before reaching this point of time and then we will insert a fresh deque to each of the four arrays so that we can store more points in future.
 Finally show the output using cv2.imshow().

- The loop ends when the user presses the key ‘q’ on the keyboard indicating the end of the process.
  After ending, we released and destroyed all the windows to avoid unnecessary use of space
