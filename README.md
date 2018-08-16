# RotNet
DCNN for automatic rotation of pictures

## The task
Imagine we have a model deployed on the cloud which performs face recognition on images sent to it.This model works great on well-oriented images, i.e. images which are the right way up. However, when badly-oriented images are sent, e.g. upside-down images, the model performs poorly. Since we have no  control over how the images are sent and have no guarantee that the images will come with orientation-metadata, we would like a pre-processing step which fixes the orientation of the images before being sent to the main model. The possible orientations are 0, 90, 180, 270 degrees with respect to the refence orientation.

## Data
The dataset of faces (merge of many dataset freely available on the web, [link](http://www.face-rec.org/databases/)) is available at my Kaggle profile page [here](https://www.kaggle.com/gasgallo/faces-data-new). It's a collection of around 8k pictures of different individuals in different poses. The amount of data should be enough to train the output layer of a pre-trained model.
Moreover, the compiled model, to use for inference, is available as well at my FloydHub account [here](https://www.floydhub.com/gasgallo/datasets/rotnet_resnet/).

## Build a model
It's easy to test some pre-trained models and adjust the output layer to do the rotation classfication job. So this is the approach used to solve the problem ([reference](https://www.cs.toronto.edu/~guerzhoy/oriviz/crv17.pdf)).
To run the training launch the script `training.py`. The data for the training was generated by randomly rotating original images and taking note of the related label with some helper functions (see `utils.py`).
In this case (face pictures only), the better performing model has been a ResNet50 pre-trained model with a fully connected layer with 4 neurons to produce the classification into one of the four classes (0, 90, 180, 270). Achieved accuracy on the validation set is around 91%.

## Create an API
You can evaluate the model with `evaluate.py` script to correct the orientation of any image. You can run it as follows:

`python evaluate.py <path_to_hdf5_model> <path_to_input_image_or_directory>`

You can also specify the following command line arguments:
- `-o, --output` to specify the output image or directory.

## Test units

## Deployment
To host the model and make available the API directly in the cloud, I've decided to use the free service provided by FloydHub. Indeed, I've created and setup the service with the help of [flask](http://flask.pocoo.org/) and the backend framework of FloydHub that deployed my model into a Docker container.
The service is publicly available and can be tested by anyone without the need of having a Python machine. You can run the inference on the model as follows:

`curl -o <path_to_output_image> -F "file=@<path_to_input_image>" https://www.floydlabs.com/serve/VoRX5sWT3rg8d4VFdKh8xW`

## Considerations
The whole repository has been built in less than 48 hours. Many improvements are still possible, for example:
- find/build a better dataset to train the model and improve performances on test images
- try different approches than DCNN. Maybe simpler models could do a good job anyway due to the fact that input images are faces only. The [dlib](http://dlib.net/) library provides many interesting objects.
