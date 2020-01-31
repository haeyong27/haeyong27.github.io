---
layout: post
title:  "S3 signed URL image upload(Django 서버 부하줄이기) 2"
date:   2020-01-31
categories: AWS
tag: [AWS]
---


# image upload flow 1

![alt text](https://docs.aws.amazon.com/AmazonS3/latest/API/images/s3_post.png)

파일을 나의 웹 서버에 올리고 서버에서 다시 S3로 파일을 전송하여 저장하도록 한다면 서버에 부하가 걸릴것이다. 웹 클라이언트에서 바로 S3로 올리면 해결될거같다. 

하지만 아무나 올릴 수 있으면 누군가 이를 악용하여 개인 저장소처럼 사용할 수 있으니 인증받은 사용자만 이미지를 업로드할 수 있어야한다. 인증받은 사용자는 제한된 범위안에서 저장소에 이미지를 업로드할 수 있다.

이미지를 효율적으로 업로드하기 위해 여러 블로그를 찾다가 좋은글을 발견했고 여기서 얻은 정보들을 토대로 공부를 했다. 
[Software Architecture — Image Uploading - Joseph Gefroh - Medium](https://medium.com/@jgefroh/software-architecture-image-uploading-67997101a034)

#  Image Metadata Record
We store the metadata of the image record in our database because we have to track it. There’s no sense uploading an image without a way to retrieve or manage it later. What use is uploading a photo intended to be used as a user avatar if we have no way to associate with the user record in question?

Why don’t we store the image URL? Storing the image URL is fragile, and the link is susceptible to breaking if the URL ever changes for any reason. Storing the metadata needed to construct the URL is a lot more robust — we can easily change things like the domain name, and build the appropriate URL without having any downtime.

# 이미지 파일을 업로드 하는 프로세스
* Step 1: Client request an upload URL from the server (REQUEST)
* Step 2: Client uploads the image data to the upload URL (UPLOAD)
* Step 3: Client tells the server the upload is completed (CONFIRM)
* Step 4: Server processes image in background (PROCESS)
* Step 5: Client checks image processing status (CHECK)
* Step 6: Server is done processing image, notifies client (FINALIZE)


**Step 1: Client request an upload URL from the server (REQUEST)**
바로 업로드하지 않고 업로드 url을 요청하는 이유는 서버에 직접적으로 요청하지 않기 위함이다. 서버는 URL을 만들어내는데 이것은 시간 제한이 걸려있고, 감시되고, 권한이 부여된것이다. 또한 서버가 제공한 URL은 3rd-party 서비스에 이미지를 업로드할 권한을 가진 쿼리 파라미터를 가지고있다. 

권한에 대한 검증을 수행하고 나서 서버는 업로드할 이미지에 대한 record를 만들어야 하는데 그 안에는 다음과 같은 정보들이 포함되어야 한다. : 
- the name of the file
- the type of the file
- the url of the file 
- the status( requested, uploaded, processed)
- the associations of the image (user, campaign)
- the kind of association(banner, avatar)
- write token, read token
- any other audit data 

**Step 2: Client uploads the image data to the upload URL (UPLOAD)**

URL에 이미지를 업로드한다. 

**Step 3: Client tells the server the upload is completed (CONFIRM)**

이미지 업로드가 완료됐다는것을 서버에 이전에 받은 토큰과함께 전송한다. 

**Step 4: Server processes image in background (PROCESS)**

토큰에 대한 유효성을 검사하고 업로드 요청을 확인한다. 무결성 검사와 최적화를 진행한다. 

**Step 5: Client checks image processing status (CHECK)**
Processing images takes some time, and you don’t want the client blocking a request. The client should check back occasionally to see if the processing is done.
이미지를 처리하는데 시간이 좀 걸린다.  you don’t want the client blocking a request. 
뭔소리지이게. 클라이언트를 이미지 처리가 끝났는지 주기적으로 체크해야한다. 

**Step 6: Server is done processing image, notifies client (FINALIZE)**
업로드가 완료되고난 뒤 이미지 URL을 반환한다. 이미지가 보호되고 있다면 이미지에 접근하는 URL은 나의 Server를 가리킨다. 그리고 클라이언트의 어떤 요청에 대해서도 read token을 제공해야한다. 








---
10 Dec 2019 2:10 PM
#programming/upload