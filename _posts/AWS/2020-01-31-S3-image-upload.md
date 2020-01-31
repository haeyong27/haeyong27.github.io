---
layout: post
title:  "S3 signed URL image upload(Django 서버 부하줄이기)"
date:   2020-01-31
categories: AWS
tag: [AWS]
---

클라이언트에서 장고 서버를 거쳐 S3에 이미지를 업로드하게되면 서버에 부하가 많이 걸릴것이라 판단하며 클라이언트에서 직접 S3로 올리고 그 이미지에 대한 메타정보만 장고의 데이터베이스에 저장하려한다. 

[Scalable User Uploads with Amazon S3 – The Smyth Group](https://www.thesmythgroup.com/in-development/scalable-user-uploads-with-amazon-s3/)

![alt text](https://www.thesmythgroup.com/wp-content/uploads/2019/07/architecture.png)

The key point in the architecture is that uploads never pass through your service. Web or mobile clients simply need to get a signed upload URL from your service and upload files directly to that URL. The upload URL is signed so that unauthorized clients cannot write arbitrary files into your S3 bucket(s) and your service doesn’t have to deal with receiving, processing or storing the uploads.


하지만 인증된 사용자만 업로드할 수 있게 해야하는데 클라이언트에서 처리하는 내용은 쉽게 노출될 수 있다. 이를 보완하기 위한것이 pre-signed URL이다. 


```json
{‘url’: ‘https://haeyong-django-vuejs-s3.s3.amazonaws.com/‘,
 ‘fields’: 
{‘key’: ‘html.png’,
  ‘x-amz-algorithm’: ‘AWS4-HMAC-SHA256’,
  ‘x-amz-credential’: ‘AKIAU3NMYFIM3LCWVDTX/20191211/ap-northeast-2/s3/aws4_request’,
  ‘x-amz-date’: ‘20191211T171546Z’,
  ‘policy’: ‘eyJleHBpcmF0aW9uIjogIjIwMjAtMDEtMjJUMDk6MTU6NDZaIiwgImNvbmRpdGlvbnMiOiBbeyJidWNrZXQiOiAiaGFleW9uZy1kamFuZ28tdnVlanMtczMifSwgeyJrZXkiOiAiMTIzNDUuanBnIn0sIHsieC1hbXotYWxnb3JpdGhtIjogIkFXUzQtSE1BQy1TSEEyNTYifSwgeyJ4LWFtei1jcmVkZW50aWFsIjogIkFLSUFVM05NWUZJTTNMQ1dWRFRYLzIwMTkxMjExL2FwLW5vcnRoZWFzdC0yL3MzL2F3czRfcmVxdWVzdCJ9LCB7IngtYW16LWRhdGUiOiAiMjAxOTEyMTFUMTcxNTQ2WiJ9XX0=‘,
  ‘x-amz-signature’: ‘53c371fc4a462f2fce4251ca742d62df5c06ef437b845baccb4f47b67a6d4bdf’}}
```
이런 response를 받는다.pre-signed URL을 Django 서버에서 생성한뒤 response를 받아 



```html
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  </head>
  <body>
    <!-- Copy the 'url' value returned by S3Client.generate_presigned_post() -->
    <form action="https://haeyong-django-vuejs-s3.s3.amazonaws.com/" method="post" enctype="multipart/form-data">
      <!-- Copy the 'fields' key:values returned by S3Client.generate_presigned_post() -->
      <input name="key" value="fromhtml.png" />
      <input name="x-amz-algorithm" value="AWS4-HMAC-SHA256" />
      <input name="x-amz-credential" value="AKIAU3NMYFIM3LCWVDTX/20191213/ap-northeast-2/s3/aws4_request" />
      <input name="x-amz-date" value="20191213T071320Z" />
      <input name="AWSAccessKeyId" value="AKIAU3NMYFIM3LCWVDTX" />
      <input name="policy" value="eyJleHBpcmF0aW9uIjogIjIwMTktMTItMTdUMTE6MTM6MjBaIiwgImNvbmRpdGlvbnMiOiBbeyJidWNrZXQiOiAiaGFleW9uZy1kamFuZ28tdnVlanMtczMifSwgeyJrZXkiOiAiZnJvbWh0bWwucG5nIn0sIHsieC1hbXotYWxnb3JpdGhtIjogIkFXUzQtSE1BQy1TSEEyNTYifSwgeyJ4LWFtei1jcmVkZW50aWFsIjogIkFLSUFVM05NWUZJTTNMQ1dWRFRYLzIwMTkxMjEzL2FwLW5vcnRoZWFzdC0yL3MzL2F3czRfcmVxdWVzdCJ9LCB7IngtYW16LWRhdGUiOiAiMjAxOTEyMTNUMDcxMzIwWiJ9XX0=" />
      <input name="x-amz-signature" value="4cd8084d6ae4a8dec0830e842c820fadea56fc55e397dce98b08b831ad119cee" />
    File:
      <input type="file"   name="file" /> <br />
      <input type="submit" name="submit" value="Upload to Amazon S3" />
    </form>
  </body>
</html>
```
이렇게 하니까 다음과 같은 오류가 났다. 

```html
<Error>
<link type="text/css" id="dark-mode" rel="stylesheet" href=""/>
<style type="text/css" id="dark-mode-custom-style"/>
<Code>InvalidRequest</Code>
<Message>
The authorization mechanism you have provided is not supported. Please use AWS4-HMAC-SHA256.
</Message>
<RequestId>1F41C19BB6E65F3D</RequestId>
<HostId>
dT2dfVAQtEz97HGjOTGAqbgaEkaveDv6aIk+Ho5sxD5ynygEZS8grYUBDxx3GcAIzzcyd/enCes=
</HostId>
</Error>
```

먼저 요청이 제대로 되지 않는 이유에 대한 가설을 2개를 세웠다. 
1.  HTTP요청 형식이 잘못됐을 수 있다.
```
Status Code:400 Bad Request
```

2. 리턴된 pre-signed URL 값에 문제가 있을 수 있다.

```
<Message>
The authorization mechanism you have provided is not supported. Please use AWS4-HMAC-SHA256.
</Message>
```

### 해결방법 
1번이 문제인 경우
[AWS API 요청에 서명 - AWS 일반 참조](https://docs.aws.amazon.com/ko_kr/general/latest/gr/signing_aws_api_requests.html)
그래서 AWS API요청에 서명하는 방식을 찾아봤다. 
HTTP로 AWS에 요청을 전송할 때 AWS에서 보낸 사람을 식별하기 위해 요청에 서명을 한다. HTTP요청을 수동으로 생성하는 경우에만 HTTP 요청에 서명하는 방법을 알 필요가 있다. AWS CLI나 SKD를 사용하면 액세스 키를 사용하면 요청에 자동으로 서명을 한다. 어차피 Javscript로 보낼것이기 때문에 AWS javascript SDK를 사용한다. 
[Amazon S3 클라이언트 측 암호화를 위한 AWS SDK 지원 - AWS 일반 참조](https://docs.aws.amazon.com/ko_kr/general/latest/gr/aws_sdk_cryptography.html)

2번이 문제인 경우
pre-signed URL을 생성하는 과정을 다시 살펴본다. 
[AWS SDK, CLI 및 Explorer 사용 - Amazon Simple Storage Service](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/UsingAWSSDK.html#UsingAWSSDK-sig2-deprecation)
AWS에서 API로 통신할 때 인증하는 과정에서 서명을 해야하는데 이 서명에도 버전이 있다. 2버젼과 4버젼이 있는데 2버젼은 사용이 중지되어가고 있다. 그렇기 때문에 4버전을 사용해야한다. 필자는 파이썬을 사용했으므로 Python-Boto SDK에 대한 문서를 찾아봤다. 

기본 config 파일인 boto 파일에 다음을 지정해야한다. 

```
[s3] use-sigv4 = True
```

하지만 이것도 HTML을 통한 HTTP에는 효과가 없다. 

결론 : 
1번문제의 해결방법인 Javacript SDK를 사용하겠다. 

 [Amplify JavaScript](https://aws-amplify.github.io/docs)  
 
[Getting Started](https://aws-amplify.github.io/docs/js/start)



