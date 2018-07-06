---
title: "Custom Storage Locations"
layout: article
excerpt: Follow these steps to set up custom storage locations and access them with Synapse. 
category: howto
---

<style>
#image {
    width: 100%;
}
#imageSmall {
    width: 40%;
}
</style>


# Custom Storage Locations
While Synapse provides physical storage for files (using Amazon's S3), not all data 'in' Synapse is stored on Synapse controlled locations. For example, data files can physically reside on a user-owned S3 bucket, SFTP servers, or a local file server using a proxy servers. Creating a custom storage location allows users ownership and control of their files, especially in cases where there is a large amount of data or cases where there are additional restrictions that need to be set on the data.

{% include note.html content="System metadata, annotations, and provenance records are still stored in Synapse's S3 storage." %}

## Setting Up an External AWS S3 Bucket
Follow the documentation on Amazon Web Service (AWS) site to **[Create a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html){:target="_blank"}**. 

<a href="http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html" class="btn btn-primary">View AWS Bucket Instructions</a>{:target="_blank"}

Make the following adjustments to customize it to work with Synapse:  

* When the AWS instructions prompt you to `Create a Bucket - Select a Bucket Name and Region`, use a unique name. For example, `thisisthenameofmybucket`. 
* Select the newly created bucket and click the **Properties** button. Expand the **Permissions** section and:  
    * Make sure that all the boxes (List, Upload/Delete, View Permissions, and Edit Permissions) have been checked. It should do this by default. 
    * Select the **Add bucket policy** button and copy one of the below policies (read-only or read-write permissions). Change the name of `Resource` from “synapse-share.yourcompany.com” to the name of your new bucket (twice) and ensure that the `Principal` is `"AWS":"325565585839"`. This is Synapse's account number. 

### Read-write permissions
To allow authorized Synapse users to upload data to your bucket set read-write permissions need to be set on that bucket (you allow Synapse to upload and retrieve files):

{% highlight json %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::325565585839:root"
            },
            "Action": "s3:ListBucket*",
            "Resource": "arn:aws:s3:::thisisthenameofmybucket"
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::325565585839:root"
            },
            "Action": [
                "s3:*Object*",
                "s3:*MultipartUpload*"
            ],
            "Resource": "arn:aws:s3:::thisisthenameofmybucket/*"
        }
    ]
}
{% endhighlight %}

<br/>

For **read-write** permissions, you also need to create an object that proves to the Synapse service that you own this bucket. This can be done by creating an **<a href="/assets/downloads/owner.txt" download="owner.txt">owner.txt</a>** file with your Synapse username and uploading it to your bucket. You can upload the file with the Amazon Web Console or if you have  the [AWS command line client](https://aws.amazon.com/cli/){:target="_blank"}, you can upload using the command line. 

<img id="imageSmall" src="/assets/images/ownerTxt.png">

{% tabs %}

{% tab AWScli %}
{% highlight bash %}
# copy your owner.txt file to your s3 bucket
aws s3 cp owner.txt s3://nameofmybucket/nameofmyfolder
{% endhighlight %}
{% endtab %}

{% tab Web %}

<img id="imageSmall" src="/assets/images/uploadAWS.png">

Navigate to your bucket on the Amazon Console and select **Upload** to upload your text file.
{% endtab %}

{% endtabs %}

<br/>

### Read-only permissions
If you do not want to allow authorized Synapse users to upload data to your bucket but provide read access you can change the permissions to read-only:

{% highlight json %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::325565585839:root"
            },
            "Action": "s3:ListBucket*",
            "Resource": "arn:aws:s3:::thisisthenameofmybucket"
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::325565585839:root"
            },
            "Action": [
                "s3:GetObject*",
                "s3:*MultipartUpload*"
            ],
            "Resource": "arn:aws:s3:::thisisthenameofmybucket/*"
        }
    ]
}
{% endhighlight %}

<br/>

### Make sure to enable cross-origin resource sharing (CORS)
In **Properties**, click **Edit CORS configuration**. In the resulting pop-up, edit the configuration so that Synapse is included  in the `AllowedOrigin` tag. An example of CORS content that would allow this is:
{% highlight html %}
<CORSConfiguration>
    <CORSRule>
        <AllowedOrigin>*</AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedMethod>PUT</AllowedMethod>
        <AllowedMethod>HEAD</AllowedMethod>
        <MaxAgeSeconds>3000</MaxAgeSeconds>
        <AllowedHeader>*</AllowedHeader>
    </CORSRule>
</CORSConfiguration>
{% endhighlight %}

<br/>
<br/>
For more information, please read: [How Do I Configure CORS on My Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html#how-do-i-enable-cors){:target="_blank"}

### Set S3 Bucket as Upload Location

By default, your `Project/Folder` uses Synapse storage. You can use the external bucket configured above via our programmatic clients or web client.

{% tabs %}

{% tab Python %}
{% highlight python %}
# Set storage location
import synapseclient
import json
syn = synapseclient.login()
PROJECT = 'syn12345'

destination = {'uploadType':'S3', 
               'concreteType':'org.sagebionetworks.repo.model.project.ExternalS3StorageLocationSetting',
               'bucket':'nameofyourbucket'}
destination = syn.restPOST('/storageLocation', body=json.dumps(destination))

project_destination ={'concreteType': 'org.sagebionetworks.repo.model.project.UploadDestinationListSetting', 
                      'settingsType': 'upload'}
project_destination['locations'] = [destination['storageLocationId']]
project_destination['projectId'] = PROJECT

project_destination = syn.restPOST('/projectSettings', body = json.dumps(project_destination))
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
#set storage location
library(synapser)
synLogin()
projectId <- 'syn12345'

destination <- list(uploadType='S3', 
                    concreteType='org.sagebionetworks.repo.model.project.ExternalS3StorageLocationSetting',
                    bucket='nameofyourbucket')
destination <- synRestPOST('/storageLocation', body=toJSON(destination))

projectDestination <- list(concreteType='org.sagebionetworks.repo.model.project.UploadDestinationListSetting', 
                           settingsType='upload')
projectDestination$locations <- list(destination$storageLocationId)
projectDestination$projectId <- projectId

projectDestination <- synRestPOST('/projectSettings', body=toJSON(projectDestination))
{% endhighlight %}
{% endtab %}

{% tab Web %}
 Navigate to your **Project/Folder -> Tools -> Change Storage Location**. In the resulting pop-up, select the `Amazon S3 Bucket` option and fill in the relevant information, where Bucket is the name of your external bucket, Base Key is the name of the folder in your bucket to upload to, and Banner is a short description such as who owns the storage location:

<img id="image" src="/assets/images/external_s3.png">

{% endtab %}

{% endtabs %}


<br/>

### Adding Files in Your S3 Bucket to Synapse

If your bucket is set for read-write access, files can be added to the bucket using the standard Synapse interface (web or programmatic). 

If the bucket is read-only or you already have content in the bucket, you will have to add representations of the files in Synapse programmatically. This is done using a `FileHandle`, which is a Synapse representation of the file. 

{% tabs %}

{% tab Python %}
{% highlight python %}
# create filehandle
fileHandle = {'concreteType': 'org.sagebionetworks.repo.model.file.S3FileHandle', 
              'fileName'    : 'nameOfFile.csv',
              'contentSize' : "sizeInBytes",
              'contentType' : 'text/csv',
              'contentMd5' :  'md5',
              'bucketName' : destination['bucket'],
              'key' : 's3ObjectKey',
              'storageLocationId': destination['storageLocationId']}
fileHandle = syn.restPOST('/externalFileHandle/s3', json.dumps(fileHandle), endpoint=syn.fileHandleEndpoint)

f = synapseclient.File(parentId=PROJECT, dataFileHandleId = fileHandle['id'])

f = syn.store(f)
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
# create filehandle
fileHandle <- list(concreteType='org.sagebionetworks.repo.model.file.S3FileHandle', 
                   fileName    = 'nameOfFile.csv',
                   contentSize = 'sizeInBytes',
                   contentType = 'text/csv',
                   contentMd5 =  'md5',
                   storageLocationId = destination$storageLocationId,
                   bucketName = destination$bucket,
                   key ='s3ObjectKey')
fileHandle <- synRestPOST('/externalFileHandle/s3', body=toJSON(fileHandle), endpoint = synapseFileServiceEndpoint())

f <- File(dataFileHandleId=fileHandle$id, parentId=projectId)

f <- synStore(f)
{% endhighlight %}
{% endtab %}

{% endtabs %}

<br/>

Please see the [REST docs](http://docs.synapse.org/rest/org/sagebionetworks/repo/model/project/ExternalS3StorageLocationSetting.html){:target="_blank"} for more information on setting external storage location settings using our REST API.

## Using SFTP

To setup an SFTP as a storage location, the settings on the `Project` need to be changed, specifically the `storageLocation` needs to be set. This is best done using either R or Python but has alpha support in the web browser.
Customize the code below to set the storage location as your SFTP server:

{% tabs %}

{% tab Python %}
{% highlight python %}
import synapseclient
import json
syn = synapseclient.login()

destination = { "uploadType":"SFTP",
    "concreteType":"org.sagebionetworks.repo.model.project.ExternalStorageLocationSetting",
    "description":"My SFTP upload location", 
    "supportsSubfolders":True,
    "url":"sftp://your-sftp-server.com",
    "banner":"A descriptive banner, tada!"}

destination = syn.restPOST('/storageLocation', body=json.dumps(destination))

project_destination = {"concreteType":"org.sagebionetworks.repo.model.project.UploadDestinationListSetting", 
    "settingsType":"upload"}
project_destination['projectId'] = PROJECT
project_destination['locations'] = [destination['storageLocationId']]

project_destination = syn.restPOST('/projectSettings', body = json.dumps(project_destination))
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
library(synapseClient)
synapseLogin()
projectId <- 'syn12345'

destination <- list(uploadType='SFTP', 
                    concreteType='org.sagebionetworks.repo.model.project.ExternalStorageLocationSetting',
                    description='My SFTP upload location',
                    supportsSubfolders=TRUE,
                    url='https://your-sftp-server.com',
                    banner='A descriptive banner, tada!')
                    
destination <- synRestPOST('/storageLocation', body=destination)

projectDestination <- list(concreteType='org.sagebionetworks.repo.model.project.UploadDestinationListSetting', 
                           settingsType='upload')
projectDestination$locations <- list(destination$storageLocationId)
projectDestination$projectId <- projectId

projectDestination <- synRestPOST('/projectSettings', body = projectDestination)

{% endhighlight %}
{% endtab %}

{% endtabs %}

## Using a Proxy to Access a Local File Server or SFTP Server

For files stored outside of Amazon, an additional proxy is needed to validate the pre-signed URL and then proxy the requested file contents.  View more information **[here](https://github.com/Sage-Bionetworks/file-proxy/wiki){:target="_blank"}** about the process as well as about creating a [local proxy](https://github.com/Sage-Bionetworks/file-proxy/wiki/Setup-Proxy-Local){:target="_blank"} or a [SFTP proxy](https://github.com/Sage-Bionetworks/file-proxy/wiki){:target="_blank"}.

#### Set Project Settings for a Local Proxy
You must have a key ("your_secret_key") to allow Synapse to interact with the filesystem. 

{% tabs %}

{% tab Python %}
{% highlight python %}
import synapseclient
import json
syn = synapseclient.login()
PROJECT = 'syn12345'

destination = {"uploadType":"PROXYLOCAL", 
               "secretKey":"your_secret_key", 
               "proxyUrl":"https://your-proxy.prod.sagebase.org", 
               "concreteType":"org.sagebionetworks.repo.model.project.ProxyStorageLocationSettings"}
destination = syn.restPOST('/storageLocation', body=json.dumps(destination))

project_destination ={"concreteType": "org.sagebionetworks.repo.model.project.UploadDestinationListSetting", 
                      "settingsType": "upload"}
project_destination['locations'] = [destination['storageLocationId']]
project_destination['projectId'] = PROJECT

project_destination = syn.restPOST('/projectSettings', body = json.dumps(project_destination))
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
library(synapser)
synLogin()
projectId <- 'syn12345'

destination <- list(uploadType='PROXYLOCAL', 
                    secretKey='your_secret_key', 
                    proxyUrl='https://your-proxy.prod.sagebase.org', 
                    concreteType='org.sagebionetworks.repo.model.project.ProxyStorageLocationSettings')
destination <- synRestPOST('/storageLocation', body=toJSON(destination))

projectDestination <- list(concreteType='org.sagebionetworks.repo.model.project.UploadDestinationListSetting', 
                           settingsType='upload')
projectDestination$locations <- list(destination$storageLocationId)
projectDestination$projectId <- projectId

projectDestination <- synRestPOST('/projectSettings', body=toJSON(projectDestination))
{% endhighlight %}
{% endtab %}

{% endtabs %}


#### Set Project Settings for a SFTP Proxy

You must have a key ("your_secret_key") to allow Synapse to interact with the filesystem. 

{% tabs %}

{% tab Python %}
{% highlight python %}
import synapseclient
import json
syn = synapseclient.login()
PROJECT = 'syn12345'

destination = {"uploadType":"SFTP", 
               "secretKey":"your_secret_key", 
               "proxyUrl":"https://your-proxy.prod.sagebase.org", 
               "concreteType":"org.sagebionetworks.repo.model.project.ProxyStorageLocationSettings"}
destination = syn.restPOST('/storageLocation', body=json.dumps(destination))

project_destination ={"concreteType": "org.sagebionetworks.repo.model.project.UploadDestinationListSetting", 
                      "settingsType": "upload"}
project_destination['locations'] = [destination['storageLocationId']]
project_destination['projectId'] = PROJECT

project_destination = syn.restPOST('/projectSettings', body = json.dumps(project_destination))
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
library(synapser)
synLogin()
projectId <- 'syn12345'

destination <- list(uploadType='SFTP', 
                    secretKey='your_secret_key', 
                    proxyUrl='https://your-proxy.prod.sagebase.org', 
                    concreteType='org.sagebionetworks.repo.model.project.ProxyStorageLocationSettings')
destination <- synRestPOST('/storageLocation', body=toJSON(destination))

projectDestination <- list(concreteType='org.sagebionetworks.repo.model.project.UploadDestinationListSetting', 
                           settingsType='upload')
projectDestination$locations <- list(destination$storageLocationId)
projectDestination$projectId <- projectId

projectDestination <- synRestPOST('/projectSettings', body=toJSON(projectDestination))
{% endhighlight %}
{% endtab %}

{% endtabs %}

