snapper
=======

A simple screenshot and file upload tool for linux that works directly with S3.


Dependencies
------------

* slop - https://github.com/naelstrof/slop
* imagemagick - https://www.imagemagick.org
* s3cmd - http://s3tools.org/s3cmd
* xsel - http://www.vergenet.net/%7Econrad/software/xsel/


Installation
------------

Clone this repo

    git clone git@github.com:gilbertw1/snapper.git

Place ```snapper.conf``` in ```~/.config``` and edit it with required values

    cp snapper.conf ~/.config/snapper.conf
    

Usage
-----

### Take a screenshot

Snapper can be used to take a screenshot and allows you to select a portion of the screen to save and upload. Additionally, a single window can be screenshotted by simply clicking on the window. Once the screenshot has been saved and upload a notification will popup saying the screenshot has been upload and a url to the screenshot will be saved in the clipboard.

Run:

    ./snapper
   
   
### Upload a file

Snapper can be easily used to upload a file. When ran snapper will upload the file to S3 and upon completion will show a notification message and a url to the uploaded file will be available in the clipboard. Note that snapper retains the name of the file and adds a unique string to it to prevent filename collisions.

Run:

    ./snapper-upload <file>


Configuration
-------------

The snapper configuration file should be placed at ```~/.config/snapper.conf```. It is simply a shell script that is sourced by snapper. It's values are as follows:

* ```SNAPPER_DIR``` - Directory snapper uses to save screenshots it uploads
* ```SNAPPER_HOST``` - Optional value to be set when using a custom domain
* ```SNAPPER_BUCKET``` - S3 bucket to upload files and screenshots to
* ```SNAPPER_AWS_KEY``` - AWS access key used to authenticate with S3 (more info [here](https://aws.amazon.com/developers/access-keys/))
* ```SNAPPER_AWS_SECRET``` - AWS secret used to authenticate with S3


S3 Setup
--------

Snapper requires an S3 bucket to function. Info on creating S3 buckets can be found here: http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html. Once a bucket has been created, if you want the snaps to be publicly accessible the following policy should be added to the bucket:

    {
      "Version": "2008-10-17",
      "Statement": [{
        "Sid": "AllowPublicRead",
        "Effect": "Allow",
        "Principal": { "AWS": "*" },
        "Action": ["s3:GetObject"],
        "Resource": ["arn:aws:s3:::BUCKET_NAME/*" ]
      }]
    }
    
* Replace ```BUCKET_NAME``` with the name of your bucket.


Custom Domain Setup
-------------------

In order for Snapper to work correctly with a custom domain, a few additional steps need to be taken.

* Create an S3 bucket with the name of the custom domain. For example if I want my snaps available at ```snap.mydomain.com```, then the S3 bucket I create needs to be named ```snap.mydomain.com```.

* Enable static website hosting for the bucket. This can be done by selecting the bucket and checking "Enable website hosting" under the "Static Website Hosting" section in the bucket properties. Note that you will need at least a blank html file in the bucket to select as the required 'Index Document'.

* Add a cname record to your DNS pointing to the static S3 website endpoint (this can be found in the "Static Website Hosting" properties section). In the above example, in the DNS settings for ```mydomain.com```, a cname entry for ```snap``` would be added pointing to the static S3 site.

* Update the ```~/.config/snapper.conf``` file with the variable ```SNAPPER_HOST``` pointing to your custom domain (```snap.mydomain.com````)
