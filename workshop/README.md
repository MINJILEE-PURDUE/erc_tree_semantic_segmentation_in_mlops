# PMRI Workshop Info

This research is presented at the Purdue Military Research Institute (PMRI) to educate US and Korean military soldiers on the importance of segmentation in computer vision. The presentations take place during the symposium held at Purdue University in West Lafayette, IN, USA, on June 25-26, 2024.

## Symposium Overview

The PMRI focuses on collaborative efforts with military partners to advance research across various disciplines. Established in Fall 2014, PMRI offers opportunities such as merit-based fellowships for active-duty military, summer intern programs, and faculty exchanges, enhancing defense and security leadership through STEM and non-STEM disciplines.

### Symposium Objectives

- Build a network to promote PMRI-related goals.
- Showcase research by PMRI students, faculty, and stakeholders.
- Highlight collaborative experiences with partner institutions.

### Symposium Topics

Abstract submissions are invited on topics including emerging defense research, AI and autonomy, and defense-specific areas. For more details, see [PMRI Symposium Topics](https://www.emeraldgrouppublishing.com/how-to/authoring-editing-reviewing/write-article-abstract).

### Additional Links

- [Purdue Military Research Institute (PMRI)](https://engineering.purdue.edu/PMRI)

## Semantic Segmentation with SAM

This section includes a demonstration of using the Segment Anything Model (SAM) for semantic segmentation, highlighting its application in computer vision research presented at the symposium.

```python
import numpy as np
import torch
import matplotlib.pyplot as plt
import cv2
```
#### Function to display a mask overlay on an image
```python
def show_mask(mask, ax, random_color=False):
    if random_color:
        color = np.concatenate([np.random.random(3), np.array([0.6])], axis=0)
    else:
        color = np.array([30/255, 144/255, 255/255, 0.6])
    h, w = mask.shape[-2:]
    mask_image = mask.reshape(h, w, 1) * color.reshape(1, 1, -1)
    ax.imshow(mask_image)
```
#### Function to display points on an image/to display a bounding box on an image.
```python
def show_points(coords, labels, ax, marker_size=375):
    pos_points = coords[labels==1]
    neg_points = coords[labels==0]
    ax.scatter(pos_points[:, 0], pos_points[:, 1], color='green', marker='*', s=marker_size, edgecolor='white', linewidth=1.25)
    ax.scatter(neg_points[:, 0], neg_points[:, 1], color='red', marker='*', s=marker_size, edgecolor='white', linewidth=1.25)   
    
def show_box(box, ax):
    x0, y0 = box[0], box[1]
    w, h = box[2] - box[0], box[3] - box[1]
    ax.add_patch(plt.Rectangle((x0, y0), w, h, edgecolor='green', facecolor=(0,0,0,0), lw=2))
```
#### Load and convert the image from BGR to RGB format then create subplots and display the original image.
```python
# Load and display the image
image = cv2.imread('/home/minjilee/erc_tree_semantic_segmentation_in_mlops/tests/src/test_image_01.jpg')
image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

# Create subplots for displaying images and masks in one row
fig, axs = plt.subplots(1, 5, figsize=(25, 10))  # Adjusted figsize to make room for 5 subplots

# Plot the original image with title
axs[0].imshow(image)
axs[0].set_title('Original Image')
axs[0].axis('on')

import sys
sys.path.append("..")
from segment_anything import sam_model_registry, SamPredictor

```
#### Load the SAM model and move it to the specified device.
```python
sam_checkpoint = "/home/minjilee/erc_tree_semantic_segmentation_in_mlops/weights/sam_vit_h_4b8939.pth"
model_type = "vit_h"

device = "cuda" if torch.cuda.is_available() else "cpu"

sam = sam_model_registry[model_type](checkpoint=sam_checkpoint)
sam.to(device=device)

predictor = SamPredictor(sam)

predictor.set_image(image)

```
#### Define the coordinates of the input point and its label.
```python
input_point = np.array([[1000, 1030]])
input_label = np.array([1])

# Plot with input point and title
axs[1].imshow(image)
show_points(input_point, input_label, axs[1])
axs[1].set_title('Image with Input Point')
axs[1].axis('on')

# Perform prediction
masks, scores, logits = predictor.predict(
    point_coords=input_point,
    point_labels=input_label,
    multimask_output=True,
)

# Plot masks and points with titles
num_masks = len(masks)
for i in range(min(num_masks, 3)):  # Display up to 3 masks
    axs[i+2].imshow(image)
    show_mask(masks[i], axs[i+2])
    show_points(input_point, input_label, axs[i+2])
    axs[i+2].set_title(f'Mask {i+1}, Score: {scores[i]:.3f}')
    axs[i+2].axis('off')

# Adjust layout and display all subplots
plt.tight_layout()
plt.show()
```


![demo_erc_1](/assets/demo_01.png)
![demo_erc_2](/assets/demo_02.png)
![demo_erc_3](/assets/demo_03.png)
![demo_erc_4](/assets/demo_04.png)

![demo_erc_1](/workshop/src/workshop_demo_erc_1.png)
![demo_erc_2](/workshop/src/workshop_demo_erc_2.png)


![demo_tank_1](/workshop/src/workshop_demo_tank_1.png)
![demo_tank_2](/workshop/src/workshop_demo_tank_2.png)

This code demonstrates the usage of SAM for semantic segmentation, which can be particularly beneficial in various defense-related applications by enhancing object detection and recognition capabilities. Available contact: lee3450@purdue.edu
