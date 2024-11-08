---
title: Download Postman Collection
layout: dev_guide
nav_order: 1
has_children: false
---

## Installing Postman

1. **Download Postman**: Visit the [Postman website](https://www.postman.com/downloads/) and download the appropriate version for your operating system.
2. **Install Postman**: Follow the installation instructions for your operating system to install Postman.

## Downloading the Postman Collection

1. **Download the Collection**: Click [here](/api/pps-public.postman_collection.zip) to download the Postman collection.
2. **Unzip the Collection**: Extract the contents of the downloaded zip file to a location on your computer.

## Importing the Collection into Postman

1. **Open Postman**: Launch the Postman application.
2. **Import the Collection**:
    - Click on the `Import` button in the top-left corner of the Postman interface.
    - Select the `Upload Files` link.
    - Navigate to the location where you extracted the zip file and select the `.json` file to import the collection.

## Updating Variables with Your Credentials

1. **Get Your Credentials**: Obtain your username and password from PitchPoint. If you plan to order a product, you may need to verify the model name of the product matches the one in the Postman file.
2. **Update Variables**:
    - In Postman, double-click on the imported `pps-public` collection.
    - Click on the `Variables` tab.
    - Update the `pps_username` and `pps_password` variables with your credentials. 

By following these steps, you will have Postman set up with the PitchPoint Postman collection, and you will be ready to start making API requests.
