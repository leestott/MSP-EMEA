# Rock Paper Scissors Lizard Spock: AI Edition 
# Microsoft EMEA Summit Workshop 

## Introduction:
Everyone knows the game Rock Paper Scissors, but have you heard of Rock Paper Scissors Lizard Spock?
This twist on the game became popular through the show the Big Bang Theory ([Youtube clip](https://www.youtube.com/watch?v=Kov2G0GouBw)).

Give the game a try for yourself:
https://rockpaperscissorslizardspock.dev/!

## Objective
Perhaps you noticed when visiting the above website that it is possible to play this game with your webcam!
*Wouldn't it be cool if we could build this ourselves?*

**In this workshop we will be using AI to automatically detect the hand gestures of the game. It is much easier than you think!**
Afterwards you can claim you used machine learning to train a convolutional neural network (CNN).

## Training Data
For every machine learning model we need lots of good data to train the model on. To produce the best results for everyone here **I need your help!**
Please visit this website to submit images: **https://mspvision.azurewebsites.net/**

*What happened under the hood? We won't cover this in this workshop, but since you were curious: The JavaScript app sent images and tags to a serverless app hosted on Azure Functions. From there the images were stored in an Azure BlobStore Container for future processing. Additionally, images were directly uploaded to Azure Custom Vision.*
*More details at https://github.com/leestott/MSPEMA/rockpaperscissors and https://github.com/leestott/MSPEMEA/rockpaperscissorsapi*

## Training the Model
We log into the Azure Custom Vision Portal at https://customvision.ai
You can see that all the images you submitted have already been tagged with labels.
Now we can simply train a machine learning model with **One button!**

## Testing the Model
Using the QuickTest feature we choose the latest training iteration and upload an image. Does the result look good? If not, let's get more images and train again!

## Publishing the Model
Now we can publish the model. This allows us to **export the model** or consume it directly via a **Prediction API**.

## Let's move to prediction in the cloud
Let's use the cloud based prediction API endpoint to determine the hand gesture in an image. Take a look at the simple code to do this:
https://github.com/leestott/MSPEMEA/AzureCustomVision-Prediction 

To run this app we must set a few environment variables.

```bash
# Change these to the value corresponding to your Custom Vision Service instance and project
export Prediction_Endpoint='https://rpscustomvision-prediction.cognitiveservices.azure.com/'
export Prediction_Key='XXXXXXXXXXXXXXXXXXXXXXXXXX'
export Project_Id='2861e073-dbfa-48f9-ae13-9c0e69ff4383' 
export Iteration_Name='v2'
```
This app is much faster!

## Deploying the app to the cloud
Let's make the app available to everyone. Using Azure App Service we can easily publish this app. There are many ways but I prefer the Azure Command-Line Interface (CLI).

```bash
# F1 is the free tier!
# Choose a unique name like YOURCOOLNAME for your app, the app will be available at YOURCOOLNAME.azurewebsites.net
az webapp up --sku F1 -n rockpaperscissorslizardspock -l westus2
```

That command provided a bunch of information. Pay attention to the resource group name - we'll need that! We still need to inject our environment variables with the credentials into the app!

```bash
# -g is short hand for the resource group name
# -n is short hand for the name of the resource you are creating or modifying
az webapp config appsettings set -g beverst_rg_Linux_westus2 -n rockpaperscissorslizardspock --settings "Prediction_Key=XXXXXXXXXXXXXXXXXXX" "Prediction_Endpoint=https://rpscustomvision-prediction.cognitiveservices.azure.com/" "Project_Id=2861e073-dbfa-48f9-ae13-9c0e69ff4383" "Iteration_Name=v2"
```

For good measure let's restart the app.

```bash
az webapp restart -g beverst_rg_Linux_westus2 -n rockpaperscissorslizardspock
```

*Now the app is available at https://rockpaperscissorslizardspock.azurewebsites.net*

![Spock](https://vignette.wikia.nocookie.net/bigbangtheory/images/a/a7/Spock.jpg/revision/latest?cb=20101114221317)

**Live Long and Prosper!**

# Bonus

## Exporting the Model for TensorFlow (Python)
We choose the export option and pick TensorFlow Android (it's confusing, I know!), then pick TensorFlow. Once ready we'll download a zip file containing two important files. Our tags (`labels.txt`) and our model (`model.pb`).

Note that there are many more choices for exporting the model.

## Making prediction with the exported model in TensorFlow (Python)
This is a great option if you want to include the exported model on a device with no internet connection, or perhaps want to minimize latency but at the cost of increased CPU and Memory requirements.

We'll review this project that has the finished code for serving a TensorFlow based API that makes predictions using our ML model: https://github.com/berndverst/AzureCustomVision-TensorFlowImageClassification


# References & Resources:

Azure:
- [Azure for Students](https://azure.microsoft.com/free/students/?WT.mc_id=customvisionclassification-github-beverst): $100 / year free Azure Credit for Students

Documentation:
- [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home?WT.mc_id=customvisionclassification-github-beverst)
  - [Use an exported model in tensorflow](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-model-python?WT.mc_id=customvisionclassification-github-beverst)
  - [Use the Prediction API for the hosted classification model](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/python-tutorial?WT.mc_id=customvisionclassification-github-beverst)
- [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/containers/quickstart-python?tabs=bash&WT.mc_id=customvisionclassification-github-beverst): Host Python web applications

Github repos:
- https://github.com/berndverst/AzureCustomVision-TensorFlowImageClassification
- https://github.com/berndverst/AzureCustomVision-Prediction
- https://github.com/berndverst/rockpaperscissors
- https://github.com/berndverst/rockpaperscissorsapi
- https://github.com/microsoft/rockpaperscissorslizardspock
- https://github.com/jimbobbennett/Hackathon-CaptureImageForFaceDetection