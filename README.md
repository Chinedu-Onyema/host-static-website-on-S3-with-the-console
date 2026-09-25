# Hosting a Static Website using AWS Command Line Interface (CLI)

This repository contains instructions and scripts for hosting a static website using Amazon S3 and the AWS CloudShell. 
This approach allows you to deploy your website entirely from a web browser without installing the AWS CLI on your local machine.

### PDF GUIDE: [HOSTING A STATIC WEBSITE ON AMAZON S3 USING THE AWS COMMAND LINE INTERFACE.pdf](https://github.com/user-attachments/files/32658338/4.HOSTING.A.STATIC.WEBSITE.ON.AMAZON.S3.USING.THE.AWS.COMMAND.LINE.INTERFACE.pdf)

### WATCH VIDEO WALKTHROUGH HERE: https://youtu.be/OIHZ5_Xy0eU

## Prerequisites
Before you begin, ensure you have an [AWS Account](https://aws.amazon.com/).

## Walkthrough Steps

Follow these steps sequentially to deploy your static website.

### Step 1: Open AWS CloudShell

1. Log in to your AWS Management Console.
2. Locate the **CloudShell** icon in the navigation bar (usually in the top right corner).
3. Click it to open the terminal. You can use any region supported by CloudShell.

### Step 2: Create an S3 Bucket

Create a new S3 bucket to store your website files.

> **Note:** Bucket names must be globally unique. You must change `static-website-hosting-140023390772-bucket` to a unique name of your choice.


aws s3 mb s3://static-website-hosting-140023390772-bucket


