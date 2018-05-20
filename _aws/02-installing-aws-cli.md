---
title: "Using AWS Amplify with AWS IoT"
permalink: /aws/structure/
excerpt: "Getting things working with AWS IoT."
last_modified_at: 2018-03-20T15:19:22-04:00
---

These instructions were tested on Ubuntu 16.04 environment.

## Quick Installation

```bash
LC_ALL="en_US.UTF-8" && \
LC_CTYPE="en_US.UTF-8" && \
sudo apt install python-pip
```

## Install Python Pip

```bash
sudo apt install python-pip
```

## Setup Locale

Then setup locale of your environment. Otherwise when we installing aws cli tools, it would produce an error.

Open `/etc/environment` file and add followings to the end of the file.

```bash
LC_ALL="en_US.UTF-8"
LC_CTYPE="en_US.UTF-8"
```

## Installing AWS Cli

Run following command

```bash
sudo pip install awscli --upgrade --user
```

```bash
apt-get install awscli
```

## Testing

To check whether you have successfully installed aws-cli

`aws --version`


## Create an IAM User

login to AWS IAM console. And create a user with the permissions that you are planning to use. Copy the `Access key ID` and `Secret access key`
Also make a note of your default AWS Region (eg : `eu-central-1`). You can find it from the top right corner of your AWS Console


## Configuration
User following command and enter the `Access key ID`, `Secret access key` and `Default region name`
```bash
aws configure
```

## Upload Image to s3

aws s3 cp <PATH_TO_IMAGE> s3://<YOUR_BUCKET_NAME>

## Import Image

```json
[
  {
    "Description": "Kurento OVA",
    "Format": "ova",
    "UserBucket": {
        "S3Bucket": "alianvirtualmachineimages",
        "S3Key": "Kinaps-broadcast_test.ova"
    }
}]
```

```bash
aws ec2 import-image --description "MyVM" --license-type BYOL --disk-containers file://containers.json
aws ec2 describe-conversion-tasks --region
```

