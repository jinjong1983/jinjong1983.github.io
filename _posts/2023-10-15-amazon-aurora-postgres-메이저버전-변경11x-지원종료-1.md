---
title: "Amazon Aurora Postgres 메이저버전 변경(11.x 지원종료) -1-"
date: 2023-10-15
description: "Announcement: Amazon Aurora PostgreSQL 11.x end of support is February 29, 2024  계기 회사에서 사용중인 postgres의 버전이 좀 오래되었다. 이전에 pg_cron 사용을 희망하거나 이래저래 매니지드 r"
tags: ["Aurora", "aws", "postgres", "upgrade"]
categories: ["Tech Note", "Cloud"]
---
>[Announcement: Amazon Aurora PostgreSQL 11.x end of support is February 29, 2024](https://repost.aws/ko/questions/QUqTaY2mfcTSWoioVXmxSQdQ/announcement-amazon-aurora-postgre-sql-11-x-end-of-support-is-january-31-2024)

![](/assets/img/posts/7501cef0-8de7-455c-addb-d5fb6645ec98-image.png)
![](/assets/img/posts/fa24e053-50d5-4e6b-a8e6-87ab4a384dbd-image.png)

#### 계기
회사에서 사용중인 postgres의 버전이 좀 오래되었다. 이전에 pg_cron 사용을 희망하거나 이래저래 매니지드 rdb의 사용을 좀 잘해보려고 했을 때 메이저 버전의 문제로 제약 받은 적이 있었다. 

등 떠 밀렸기 때문에 바꾸긴 해야하고, dba롤을 같이 하고 있으니 내가 가장 큰 목소리와 의사결정의 영향을 할 수 있었다.

ai에게 문의하면 아래와 같은 답변을 받을것이다.
> 1. 요구 사항 분석:
    - 현재 사용 중인 익스텐션 및 애플리케이션들의 호환성을 확인하고, 업데이트로 인한 영향을 분석합니다.
    - 변경된 기능과 업데이트로 인한 성능 향상에 대한 요구 사항을 정리합니다.
2. 테스트 환경 구축:
    - 실제 운영 환경과 유사한 테스트 환경을 구축합니다.
    - PostgreSQL 15를 설치하고, 데이터베이스를 복제하여 테스트를 진행할 수 있는 환경을 구성합니다.
3. 테스트 계획 수립:
    - 테스트 시나리오와 테스트 케이스를 작성하여 업데이트의 영향을 평가합니다.
    - 예상되는 문제 및 예외 상황에 대한 테스트 계획을 수립합니다.
4. 업데이트 시점 및 절차 결정:
    - 예상되는 작업 시간, 운영 환경에 미치는 영향 등을 고려하여 업데이트 시점을 결정합니다.
    - 롤백 전략을 수립하고, 업데이트를 진행할 절차를 정의합니다.
5. 백업 및 복구 계획 수립:
    - 업데이트 전에 데이터베이스의 완전한 백업을 수행하고, 데이터의 무결성을 확인하는 복구 계획을 수립합니다.
    - 문제가 발생할 경우에 대비하여 롤백 및 복구 절차를 준비합니다.
6. 업데이트 진행:
    - 테스트 환경에서 업데이트를 실시합니다.
    - 업데이트 과정에서 발생하는 문제를 기록하고, 필요한 조치를 취합니다.
7. 성능 튜닝 및 최적화:
    - 업데이트 후에 데이터베이스 성능을 평가하고, 필요한 성능 튜닝을 수행합니다.
    - PostgreSQL 15에서 제공하는 새로운 기능과 최적화된 설정을 적용하여 성능을 최대화합니다.
8. 지원 및 문서 확인:
    - 업데이트 후에도 적절한 지원을 받을 수 있는지 확인하고, 문제가 발생할 경우에 대비한 지원 방안을 확인합니다.
    - 업데이트에 필요한 문서와 자료를 사전에 확인하여 문제 발생 시 참고할 수 있도록 준비합니다.
9. 운영 환경으로의 배포:
    - 업데이트가 성공적으로 완료되었고, 테스트에서 발견된 문제들이 해결되었음을 확인한 후, 운영 환경으로 업데이트를 배포합니다.
    
#### 사용가능한 버전의 확인과 각 버전들간의 차이확인
[Aurora PostgreSQL용 PostgreSQL DB 엔진 업그레이드](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.PostgreSQL.html)

![](/assets/img/posts/f566e556-9646-4a6b-8eeb-a675e5374c72-image.png)

aurora 는 매니지드서비스 이기 때문에 쉬운 버전변경이 가능하며 때문에 현재 버전에서 이용가능한 버전의 목록이 별도로 존재한다. 지원가능한 버전을 확인했다면 이제 각 버전들간의 기능 차이를 확인해야 한다.

#### aws document
![](/assets/img/posts/b2f21820-5569-4da0-a797-0bef7c6eeb57-image.png)

숨이 콱 막히는 걸 알지만 어쩌겠는가... 봐야지... 하지만 치트시트가 존재한다.
바로 **feature matrix**

요런 기준으로
![](/assets/img/posts/a7eb6f73-00c0-4caf-b4b8-eab8a436593f-image.png)


요렇게 비교해준다
![](/assets/img/posts/183416cc-7103-4235-93c1-dbf471a1a686-image.png)

요약만 따로보기
![](/assets/img/posts/0b33ed08-bf48-44a0-9a10-cf91ed4c2cbf-image.png)


#### 성능차이
메이저 버전이 변경되면 당연히 성능의 차이도 고려해야한다.
[Benchmark results](https://www.enterprisedb.com/blog/performance-comparison-major-PostgreSQL-versions)

신나게 구글링하면 아래와 같은 자료도 볼 수 있다.
![](/assets/img/posts/7fce9874-40fb-4dda-8900-c7f87946b408-image.png)

#### 추가로 찾아보기
[포스트그레스큐엘: 강좌](https://postgresql.kr/blog/)
[Read Before You Upgrade: Best Practices for Choosing Your PostgreSQL Version](https://www.timescale.com/blog/read-before-you-upgrade-best-practices-for-choosing-your-postgresql-version/)
밑에 문서는 번역해서 포스팅해도 괜찮을 것 같기도...

다음 포스팅에서는 실제적으로 업그레이드 시에 필요한 사항을 작성해볼까 한다.