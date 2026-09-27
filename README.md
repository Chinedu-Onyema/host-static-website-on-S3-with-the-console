# Hosting a Static Website using AWS Command Line Interface (CLI)

This repository contains instructions and scripts for hosting a static website using Amazon S3 and the AWS CloudShell. 
This approach allows you to deploy your website entirely from a web browser without installing the AWS CLI on your local machine.

#### PDF GUIDE: [HOSTING A STATIC WEBSITE ON AMAZON S3 USING THE AWS COMMAND LINE INTERFACE.pdf](https://github.com/user-attachments/files/32658338/4.HOSTING.A.STATIC.WEBSITE.ON.AMAZON.S3.USING.THE.AWS.COMMAND.LINE.INTERFACE.pdf)

#### WATCH VIDEO WALKTHROUGH HERE: https://youtu.be/OIHZ5_Xy0eU

## PREREQUISITES
Before you begin, ensure you have an [AWS Account](https://aws.amazon.com/).

## WALKTHROGH STEPS
Follow these steps sequentially to deploy your static website.


### Step 1: Open AWS CloudShell

Log in to your AWS Management Console.
Locate the **CloudShell** icon in the navigation bar (usually in the top right corner).
Click it to open the terminal. You can use any region supported by CloudShell.


### Step 2: Create an S3 Bucket

Create a new S3 bucket to store your website files.
> **Note:** Bucket names must be globally unique. You must change `static-website-hosting-140023390772-bucket` to a unique name of your choice.
<PRE>aws s3 mb s3://static-website-hosting-140023390772-bucket</PRE>


### Step 3: Upload Website Files to CloudShell

Prepare your website files (HTML, CSS, JS) in a local folder on your computer.
ZIP this entire folder.
In the AWS CloudShell window, click the Actions menu (top right).
Select Upload File.
Select your zipped file and upload it to the CloudShell environment.


### Step 4: Verify and Unzip the Archive

1) Confirm the file was uploaded successfully (replace the filename with your actual ZIP file name):
<PRE>find ~ -name 'chinedu-onyema-website.zip'</PRE>

2) Unzip the file in the current directory
<PRE>unzip "chinedu-onyema-website.zip"</PRE>

3) Verify the contents (you should see your zipped file and the new unzipped folder):
<PRE>ls</PRE>


### Step 5: Sync Files to S3
1) Upload the unzipped website folder contents to your S3 bucket.
Important: Replace chinedu-onyema-website with the name of your unzipped folder, and adjust the bucket name to match the one created in Step 2.
<PRE>aws s3 sync ./ 'chinedu-onyema-website' s3://static-website-hosting-140023390772-bucket</PRE>

2) Confirm the files are in the bucket:
<PRE>aws s3 ls s3://static-website-hosting-140023390772-bucket</PRE>


### Step 6: Create and Configure Bucket Policy
We need to make the bucket publicly readable so visitors can view the website.
1) Create a policy file using nano:
<PRE>nano bucket_policy.json</PRE>

2) Copy and paste the following JSON into the editor.
CRITICAL: You must replace my-static-website-bucket in the Resource line with the actual name of your bucket created in Step 2.
```
{
"Version": "2012-10-17",
"Statement": [
{
"Sid": "PublicReadGetObject",
"Effect": "Allow",
"Principal": "*",
"Action": "s3:GetObject",
"Resource": "arn:aws:s3:::static-website-hosting-140023390772-bucket/*"
}
]
}
```

Save and exit nano:
Press Ctrl+O to save.
Press Enter to confirm the filename.
Press Ctrl+X to exit.
(Optional) If you wish to download and verify this file locally, run find to get the path, then use the Actions menu in CloudShell to Download File.

<PRE>find ~ -name 'bucket_policy.json'</PRE>


### Step 7: Apply Bucket Policy

1) Disable S3 Block Public Access for this bucket to permit public policies:
   
```
aws s3api put-public-access-block \
    --bucket static-website-hosting-140023390772-bucket \
    --public-access-block-configuration \
    "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
```

2) Attach the policy created in Step 6 to your bucket:
```
aws s3api put-bucket-policy \
    --bucket static-website-hosting-140023390772-bucket \
    --policy file://bucket_policy.json
```


### Step 8: Enable Static Website Hosting

Configure the bucket to serve as a static website, specifying the index and error documents.
```
aws s3 website s3://static-website-hosting-140023390772-bucket \
    --index-document index.html \
    --error-document error.html
```

### Step 9: Access Your Website
Generate the URL to view your hosted website.
<PRE>echo http://static-website-hosting-140023390772-bucket.s3-website-eu-north-1.amazonaws.com/index.html</PRE>

