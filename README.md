# RotNet
DCNN for automatic rotation of face pictures

## The task
Imagine we have a model deployed on the cloud which performs face recognition on images sent to it.This model works great on well-oriented images, i.e. images which are the right way up. However, when badly-oriented images are sent, e.g. upside-down images, the model performs poorly. Since we have no  control over how the images are sent and have no guarantee that the images will come with orientation-metadata, we would like a pre-processing step which fixes the orientation of the images before being sent to the main model. The possible orientations are 0, 90, 180, 270 degrees with respect to the refence orientation.

<p align = 'center'>
<img src = 'examples/take-home-yoyo.jpg' height = '400px'>
</p>
<p align = 'center'>
From lef to right and from top to bottom: reference orientation (0°), +90°, +180°, +270°.
</p>

## Data
The dataset of faces (merge of many dataset freely available on the web, [link](http://www.face-rec.org/databases/)) is available at my Kaggle profile page [here](https://www.kaggle.com/gasgallo/faces-data-new) and [here](https://www.kaggle.com/gasgallo/lag-dataset). It's a collection of around 8k and 4k pictures of different individuals in different poses. The amount of data should be enough to train the output layer of a pre-trained model.
Moreover, the compiled model, to use for inference, is available as well at my FloydHub account [here](https://www.floydhub.com/gasgallo/datasets/rotnet-vgg16).

## Build a model
It's easy to test some pre-trained models and adjust the output layer to do the rotation classfication job. So this is the approach used to solve the problem ([reference](https://www.cs.toronto.edu/~guerzhoy/oriviz/crv17.pdf)).
To run the training launch the script `training.py`. The data for the training was generated by randomly rotating original images and taking note of the related label with some helper functions (see `utils.py`).
In this case (face pictures only), the better performing model has been a VGG16 pre-trained model with a fully connected layer with 4 neurons to produce the classification into one of the four classes (0, 90, 180, 270). Achieved accuracy on the validation set is around 99.8%.

## Create an API
You can evaluate the model with `evaluate.py` script to correct the orientation of any image. You can run it as follows:

`python evaluate.py <path_to_hdf5_model> <path_to_input_image_or_directory>`

You can also specify the following command line arguments:
- `-o, --output` to specify the output image or directory.

## Test units
You can test the model with `test.py` script to evaluate the accuracy of the model over a given dataset. You can run it as follows:

`python test.py <path_to_hdf5_model> <path_to_input_image_or_directory>`

It returns a log of the process and the final values of:
- total accuracy
- corrupted files

## Deployment
To host the model and make available the API directly in the cloud, I've decided to use the free service provided by FloydHub. Indeed, I've created and setup the service with the help of [flask](http://flask.pocoo.org/) and the backend framework of FloydHub that deployed my model into a Docker container.
The service is publicly available and can be tested by anyone without the need of having a Python machine. You can run the inference on the model as follows:

`curl -o <path_to_output_image> -F "file=@<path_to_input_image>" <url_to_hosting_server>`

The job isn't constanly running on the server due to cost reasons. To obtain a working URL to test the code, please contact me and I'll set it up for you.

## Considerations
The whole repository has been built in less than 48 hours. Many improvements are still possible, for example:
- find/build a better dataset to train the model and improve performances on test images
- try different approches than DCNN. Maybe simpler models could do a good job anyway due to the fact that input images are faces only. The [dlib](http://dlib.net/) library provides many interesting objects (see the implementation [here](https://github.com/gasgallo/FaceRot)).
