# Eye-Blink-detection.
          Real time eye blink detection using face detector and facial landmarks from Dlib package. 

## What is eye blink exactly, and why does this happen?

Eye blinking is a suppressed process that involves the rapid closure and reopening of the eyelid. Multiple muscles are involved in the blinking of the eyes. 
The orbicularis oculi and levator palpebrae superioris are the two primary muscles that regulate eye closure and opening.

### The importance od eye blink...

Blinking serves some important purposes, one of which is to moisten the corner of an individual’s eye. 
Additionally, it cleans the cornea of the eye when the eyelashes are unable to catch all of the dust and debris that enter the eye. 
Everyone must blink to spread tears over the entire surface of the eyeball, and especially over the surface of the cornea. Blinking also performs as a reflex to prevent foreign objects from entering the eye.

## How can we detect eye blink? 
     Unlike traditional image processing methods for computing blinks ‎which ‎typically involve some combination of:‎

1- Eye localization.
2- Thresholding to find the whites of the eyes.
3- Determining if the “white” region of the eyes disappears for a period of ‎time (indicating a blink).

          This approach has a lot of limitations, and not efficient.
          "We'll not take about it's limitations right now, But Briefly "ًwhat if a person is looking down to the keyboard ?! 
          With this approache we'll get alot of false-positive detections, and this is not accaptable, and does not make satisfication"  

So, instead of this approach, to build our blink detector, we’ll compute a ***metric called the eye aspect ratio (EAR)***, introduced by ***Soukupová and Čech in their 2016 paper***, [Real-Time Eye Blink Detection Using Facial Landmarks](http://vision.fe.uni-lj.si/cvww2016/proceedings/papers/05.pdf). 
The EAR is instead a much more elegant solution that involves a very simple calculation based on ***the ratio of distances between facial landmarks of the eyes***. 
This method for eye blink detection is fast, efficient, and easy to implement.

### Steps involved:
•	Face detection to localize the face in the image.
Face detection is the first method that locates a human face and returns a ‎value in x, y, w, 1h (the face ROI) which is a rectangle.‎
•	Detect the key facial landmark structures on the face ROI. 
After getting the location of a face in an image, we have to through points ‎inside the rectangle. ‎
•	Extract out the eyes’ landmarks. 
The eyes are our region of interest.
•	Calculate eye aspect ratio (EAR). 
EAR is a scalar value that responds, especially for opening and closing eyes
•	Threshold the EAR to determine if the person is blinking.
A quick overview with this psodocode of our approach approach to identifying eye blink using this flow chart to illustrate this approach.

<img src= "https://user-images.githubusercontent.com/84151016/176217478-60936b52-7237-4cf2-9333-5e4678b34114.jpg" width=900, height=700>

We assume that we already have the trained face detector and face landmark detector.

The EAR formula is as follows: 

<img src= "https://user-images.githubusercontent.com/84151016/176219075-88b39950-a9bf-466a-ba22-b491687fd073.png" width=700, height= 500>

          Where p1, …, p6 are 2D facial landmark locations. 

This method uses just the EAR as the metric to determine if a person has ‎blinked or not. 

We are only interested in two sets of facial structures of the eyes. 
Where each eye is represented by 6 (x, y)-coordinates, starting at the left-corner ‎of the eye (as if you were looking at the person, and then working clockwise around the remainder of the region):‎
 
 
<img src= "https://user-images.githubusercontent.com/84151016/176220711-d7c21475-5e46-4a29-8039-696ab7c43ca0.jpg" width=700, height= 500>

Based on this image, we should take away on key point:

The numerator of the equation above computes the distance between the ‎vertical eye landmarks while the denominator computes the distance between ‎horizontal eye landmarks, weighting the denominator appropriately since there ‎is only one set of horizontal points but two sets of vertical points.

## Why is this equation so interesting? 
 
Well, as the paper found out that the EAR will remain approximately constant when the eyes are open and ‎then will rapidly approach zero during a blink, 
then increase again as the eye opens.
Using this simple equation, we can avoid image processing techniques and simply rely on the ratio of eye landmark distances to determine if a person is blinking.
 
 <img src= "https://user-images.githubusercontent.com/84151016/176221233-7eefc6e5-fd02-4d96-b081-47ae13b83af7.jpg" width= 600, height= 400>

 <img src= "https://user-images.githubusercontent.com/84151016/176221277-8216968a-b701-4470-9126-825626fc57fb.jpg" width= 600, height= 300 >

On the top-left we have an eye that is fully open - the EAR here would be larger ‎and relatively constant over time. However, once the person blinks (on the top-‎right) the eye aspect ratio decreases dramatically, approaching zero. 
The bottom figure plots a graph of the EAR over time for a video clip. As we can ‎see, the eye aspect ratio is constant, then rapidly drops close to zero, then ‎increases again, indicating a single blink has taken place. 
The corresponding image should illustrate the idea we’ve talked about.‎
 
<img src= "https://user-images.githubusercontent.com/84151016/176221465-9d2e26e3-de82-4450-9237-4086f6a3aff3.png" width= 700, height= 400 >

### How can we distinguish the normal blink from blink in blink to speak language?‎

We have a ***very good challenge with the blink rate***, as the term “eye blink” refers ‎to the quick shutting and reopening of the eyelids, which normally lasts ‎between 100 and 400 MS. 
Reflex blinking occurs significantly faster than spontaneous blinking, which occurs ‎significantly less frequently.
Which serves as a very procedural point to distinguish between the normal and ‎usually blinks and the required blink as a basic letter in blink to speak language. 

Here we can look at pseudocode ‎to enhance understanding of our approach.‎

<img src= "https://user-images.githubusercontent.com/84151016/176221643-03c98442-a35a-4a66-b07c-17e76a28eea8.jpg" width= 700, height= 400 >


## The limitations of this approach.
1- However, due to noise in the video stream, subpar facial landmark detections, or fast changes in viewing angle, 
this approach could produce false-positive detections, reporting that a blink has taken place when in ‎reality the person had not blinked.‎

To make this system more robust, Soukupová and Čech recommened that:‎

•	Computing the eye aspect ratio for the Nth frame, along with eye aspect ‎ratios ‎for N - 6 and N + 6 frames, 
then concatenating these eye aspect ratios ‎to ‎form a 13-dimensional feature vector.‎

•	Training a Support Vector Machines (SVM) or Adaboost on these feature ‎vectors.‎
This combination of temporal-based feature detector and SVM classifier helps ‎‎reduce false-positive blink detections and improves the overall accuracy of ‎the ‎blink detector.

2- You might be wondering “A person’s eye size does not match another’s ‎eye; for example, one has big eyes but the other has small ones”. 
As expected, when a person with small eyes closes his/her eyes, he/she ‎may appear to have the same eye height as a person with large eyes. This ‎issue will affect the experimental results. 
Therefore, we relied on this simple but effective technique for detecting eye ‎blink using a newly developed facial landmark detector with a modified ‎EAR. ‎

3- The blinking detection’s accuracy may be reduced in particular by the ‎shadows cast by glasses and/or poor lighting.  
As a result, some applications like drowsiness symptoms include yawning ‎and nodding in addition to the frequency of blinking. But we cannot ‎resort to this solution, because our patient cannot move, speak nor do ‎yawning, and if it’s this will get useability down.‎

## Dlib’s facial landmarks.‎
In our project, we used Dlib’s facial landmarks detector (Kazemi & Sullivan, ‎‎2014). Which is used to estimate 68 (x, y)-coordinates corresponding to facial ‎structures on the face. It has been trained on a real-world dataset and ‎demonstrate exceptional resilience to a wide range of environmental factors, ‎including lighting conditions, face emotions, and head position. For each video ‎frame, the proposed method calculates the facial landmark locations and extracts ‎the vertical distance between the eyelids using the facial landmark positions. 
Our results show that the recognizable landmarks are sufficiently accurate to ‎determine the degree of eye-opening and closing consistently. 
The proposed algorithm estimates the facial landmark positions, extracts a single ‎scalar quantity by using Modified EAR and characterizing the eye closeness in ‎each frame. 
Finally, blinks are detected by the Modified EAR threshold value and detecting eye ‎blinks as a pattern of EAR values in a short temporal window.‎
The 68 coordinates’ indices Jaw Points = 0–16, Right Brow Points = 17–21, Left ‎Brow Points = 22–26, Nose Points = 27–35, Right Eye Points = 36–41, Left Eye ‎Points = 42–47, Mouth Points = 48–60, Lips Points = 61–67.
shown in Fig. 

<img src= "https://user-images.githubusercontent.com/84151016/176220137-41ee7880-12b5-4e6f-a8af-85e213435cd9.png " width= 700, height= 400 >

### The goal of facial landmark identification. 
facial landmarks is to identify and track significant landmarks on the face. Face tracking becomes strong for rigid facial deformation ‎and not stiff due to head movements and facial expressions. Furthermore, facial landmarks were successfully applied to face alignment, head pose estimation, face ‎swapping…. ETC.‎

## What are ather Eye blink detections applications ?

Eye blinks detection is essential and has been applied in different fields in ‎technology such as the intercommunication between disabled people and ‎computers (Królak & Strumiłło, 2012). Anther application is drowsiness ‎detection (Rahman, Sirshar & Khan, 2016), Fatima et al., 2020 research ‎proposed a low-cost solution for driver fatigue detection based on micro-sleep ‎patterns. 
Based on driver’s facial expression to determine their state of drowsiness. an ‎alarm will sound if the eyes remain closed for a certain time. One more is anti-‎spoofing protection in face recognition systems (Pan et al., 2007), ……ETC.‎

## Resources

To build our blink detector, we’ll be computing a metric called the eye aspect ratio (EAR), introduced ‎by Soukupová and Čech in their 2016 paper, Real-Time Eye Blink Detection Using Facial Landmarks.‎
Paper link: http://vision.fe.uni-lj.si/cvww2016/proceedings/papers/05.pdf
https://pyimagesearch.com/2017/04/24/eye-blink-detection-opencv-python-dlib/‎
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9044337/#ref-37‎
https://github.com/ChristineDewi/Blinks-Detection
https://www.blinkingmatters.com/‎

Dlib documentation
OpenCV documentation
