---
title: "process timeout 과 TypeError: can't pickle SSLContext objects"
date: 2024-05-02
description: "기존에 만들어두었던 커다란 크기의 환경변수용 데이터 클래스를 리팩토링 하다가 위의 문장을 맞이했다.리팩토링 하면서 좀 객체지향 스럼게 몇가지 메소드 추가하면서 작업했는데 문제가 생겼다.메인에서 모듈함수를 호출하고 호출함수의 수행시간이 180초가 되면 종료시켜야 하는 니"
tags: ["multiprocessing", "pickle", "python", "sslcontext", "timeout"]
categories: ["Tech Note", "Python"]
---
기존에 만들어두었던 커다란 크기의 환경변수용 데이터 클래스를 리팩토링 하다가 위의 문장을 맞이했다.
리팩토링 하면서 좀 객체지향 스럼게 몇가지 메소드 추가하면서 작업했는데 문제가 생겼다.

![](/assets/img/posts/f3fb37e0-f722-4703-9377-59c4fe589aec-image.png)


메인에서 모듈함수를 호출하고 호출함수의 수행시간이 180초가 되면 종료시켜야 하는 니즈가 있어서
함수의 수행시간을 기준으로 강제종료하는 로직을 구현해야 했다.

linux 계열에선 sigterm 을 사용할 수 있었으나 win 계열에선 사용불가였고
비동기의 타임아웃, 멀티프로세싱의 타임아웃, 쓰레딩의 타임아웃 등의 구현사례를 찾은다음에 
직접 구현할까 하다가 꽤 많은 사람들이 이미 구현해놔서 가져다 썻다.

https://pypi.org/project/wrapt-timeout-decorator/
https://pypi.org/project/async-timeout/

요 두개가 가장 많이 쓰이는듯 하다.

어찌 됐든...
```
def func(s3_client:S3Client)-> None #boto3-stub
	pass

func(s3=SQS(env="prd",priority="priority").get_s3_client())
```
같은 식으로 구성해서 사용했는데 제목과 같은 오류를 뱉은 것
문서와 내부구조를 까서 보니.. 프로세스를 spawn 해서 함수를 태우고 해당 함수에 SSLContext가 들어가면

**python 내부에선 그걸 pickle 로 직렬화를 해서 전달하는데 SSLContext는 직렬화가 불가능해서 발생하는 오류다**
**비슷한 결에서, 두개의 프로세스가 하나의 커넥션을 주고받으면서 사용하면 안된다던가 하는 내용도 있었다. 직렬화가 안되니까.**

싱글턴 클래스로 구현해서 회피하는 방법도 있는거 같고
메모리 주소를 전달해서 접근하는 방법도 있을 거 같은데 굳이 바쁜데 그렇게 꼼꼼하게 확인 할 시간은 없어서..

당연히 코드는
```
def func(s3_object:S3)-> None #boto3-stub
	s3_client = s3_object.get_s3_client()
	pass

func(s3=SQS(env="prd",priority="priority"))
```
같은 형태로 변경했다. 객체 생성에 필요한 정보를 전달하고 해당클래스에서 s3 클라이언트 객체를 구성하는것으로 완료


참고
https://stackoverflow.com/questions/74838064/ssl-pickle-error-when-running-multiprocess-inside-classs-method-and-websocket
https://medium.com/devopss-hole/python-multiprocessing-pickle-issue-e2d35ccf96a9
https://discuss.ray.io/t/raysgd-pytorch-fail-typeerror-cant-pickle-sslcontext-objects/3921
https://medium.com/@jwnx/multiprocessing-serialization-in-python-with-pickle-9844f6fa1812