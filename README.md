# Hosting a Static Website using AWS Console & AWS Command Line Interface (CLI)

This repository contains step-by-step instructions for hosting a static website directly through the AWS Management Console and AWS CLI using Amazon S3, configuring public access settings, and making your website files publicly accessible.

#### PDF GUIDE FOR AWS CLI: [HOSTING A STATIC WEBSITE ON AMAZON S3 USING THE AWS CLI.pdf](https://github.com/user-attachments/files/32658338/4.HOSTING.A.STATIC.WEBSITE.ON.AMAZON.S3.USING.THE.AWS.COMMAND.LINE.INTERFACE.pdf)

#### PDF GUIDE FOR CONSOLE: [HOSTING A STATIC WEBSITE ON AMAZON S3 USING THE AWS CONSOLE.pdf](https://github.com/user-attachments/files/32696905/3.HOSTING.A.STATIC.WEBSITE.ON.AMAZON.S3.USING.THE.AWS.MANAGEMENT.CONSOLE.pdf)

#### WATCH VIDEO WALKTHROUGH HERE (AWS CLI): https://youtu.be/OIHZ5_Xy0eU

#### WATCH VIDEO WALKTHROUGH HERE (AWS CONSOLE): https://youtu.be/xRujItk6yHQ


## PHASE 1: HOST A STATIC WEBSITE WITH THE AWS CONSOLE

---

### PREREQUISITES

- An active [AWS Account](https://aws.amazon.com/).
- A prepared website folder containing your web assets (e.g., `index.html`, error documents, and other dependencies).

---

### STEP-BY-STEP DEPLOYMENT GUIDE

#### Step 1: Create an S3 Bucket
1. Log in to your **AWS Management Console** and type `S3` in the search bar.
2. Navigate to the **Amazon S3** dashboard, click on **General purpose bucket**, and then click **Create bucket**.
3. Configure the general settings:
   - **Bucket type:** Choose **General Purpose**.
   - **Bucket name:** Enter a globally unique name, such as `static-website-hosting-140023390772-bucket`.
4. **Object Ownership:** Select **ACLs enabled**.
5. **Block Public Access settings for this bucket:** **Uncheck** the "Block all public access" box (acknowledge the warning prompt).
6. **Bucket Key:** Select **Disable**.
7. Click **Create bucket**.

---

#### Step 2: Upload Website Files
1. After the S3 bucket has been successfully created, click on the name link of your bucket.
2. Click **Upload**, drag and drop or select your website folder containing its dependencies, and click **Upload** at the bottom.
3. *Optional Test:* After uploading, click into the uploaded folder name link, check the box next to `index.html`, select **Copy URL**, and paste it into a new browser tab. You should observe an **Access Denied** error (this is expected until permissions and hosting are configured).

---

#### Step 3: Enable Static Website Hosting
1. Go back to the root of your bucket (`static-website-hosting-140023390772-bucket`) and click on the **Properties** tab.
2. Scroll down to the bottom and locate **Static website hosting**, then click **Edit**.
3. Configure the settings:
   - **Static website hosting:** Select **Enable**.
   - **Hosting type:** Select **Host a static website**.
   - **Index document:** Enter `index.html` (or the name of your main entry file).
4. Click **Save changes**.
5. *Note:* Trying to access the website via its URL at this stage will still throw an error, as public policies and ACL permissions are not yet fully applied.

---

#### Step 4: Verify Public Access Configurations
1. Go to your bucket's **Permissions** tab.
2. Locate **Block public access (bucket settings)**, click **Edit**, ensure "Block all public access" is **unlocked/unchecked**, click **Save changes**, type `confirm`, and save.

---

#### Step 5: Make Website Objects Public via ACLs
1. Go to the **Objects** tab and click on your uploaded folder name link.
2. Select **all the files** inside that folder using the check box at the top of the list.
3. Click on the **Actions** drop-down menu at the top and select **Make public using ACL**.
4. On the confirmation page, click **Make public**.

---

#### Step 6: Access Your Live Website
1. Return to your bucket's **Properties** tab and scroll back down to **Static website hosting**.
2. Click the provided website endpoint URL. Your static website should now load successfully and be live in your browser!



## PHASE 2: HOST A STATIC WEBSITE WITH THE AWS CLI

### PREREQUISITES
Before you begin, ensure you have an [AWS Account](https://aws.amazon.com/).

### WALKTHROUGH STEPS
Follow these steps sequentially to deploy your static website.


#### Step 1: Open AWS CloudShell

Log in to your AWS Management Console.
Locate the **CloudShell** icon in the navigation bar (usually in the top right corner).
Click it to open the terminal. You can use any region supported by CloudShell.


#### Step 2: Create an S3 Bucket

Create a new S3 bucket to store your website files.
> **Note:** Bucket names must be globally unique. You must change `static-website-hosting-140023390772-bucket` to a unique name of your choice.
<PRE>aws s3 mb s3://your-S3-bucket-name</PRE>


#### Step 3: Upload Website Files to CloudShell

Prepare your website files (HTML, CSS, JS) in a local folder on your computer.
ZIP this entire folder.
In the AWS CloudShell window, click the Actions menu (top right).
Select Upload File.
Select your zipped file and upload it to the CloudShell environment.


#### Step 4: Verify and Unzip the Archive

1) Confirm the file was uploaded successfully (replace the filename with your actual ZIP file name):
<PRE>find ~ -name 'chinedu-onyema-website.zip'</PRE>

2) Unzip the file in the current directory
<PRE>unzip "chinedu-onyema-website.zip"</PRE>

3) Verify the contents (you should see your zipped file and the new unzipped folder):
<PRE>ls</PRE>


#### Step 5: Sync Files to S3
1) Upload the unzipped website folder contents to your S3 bucket.
Important: Replace chinedu-onyema-website with the name of your unzipped folder, and adjust the bucket name to match the one created in Step 2.
<PRE>aws s3 sync ./ 'chinedu-onyema-website' s3://your-S3-bucket-name</PRE>

2) Confirm the files are in the bucket:
<PRE>aws s3 ls s3://your-S3-bucket-name</PRE>


#### Step 6: Create and Configure Bucket Policy
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
"Resource": "arn:aws:s3:::your-S3-bucket-name/*"
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


#### Step 7: Apply Bucket Policy

1) Disable S3 Block Public Access for this bucket to permit public policies:
   
```
aws s3api put-public-access-block \
    --bucket your-S3-bucket-name \
    --public-access-block-configuration \
    "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
```

2) Attach the policy created in Step 6 to your bucket:
```
aws s3api put-bucket-policy \
    --bucket your-S3-bucket-name \
    --policy file://bucket_policy.json
```


#### Step 8: Enable Static Website Hosting

Configure the bucket to serve as a static website, specifying the index and error documents.
```
aws s3 website s3://your-S3-bucket-name \
    --index-document index.html \
    --error-document error.html
```

#### Step 9: Access Your Website
Generate the URL to view your hosted website.
<PRE>echo http://your-S3-bucket-name.s3-website-eu-north-1.amazonaws.com/index.html</PRE>

