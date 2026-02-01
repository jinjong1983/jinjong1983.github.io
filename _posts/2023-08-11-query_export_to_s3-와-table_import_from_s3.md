---
title: "query_export_to_s3 와 table_import_from_s3"
date: 2023-08-11
description: "업무에서 환경을 나누어서 작업을 하다보면 db간의 데이터 이관 또는 파일로부터의 벌크로드를 해야 하는케이스가 굉장히 많다. 때문에 rdb들은 관련 기능들을 파일로 제공 하고 있으며 동일 목적의 기능으로는 가장 강력한 성능을 주로 보여준다. postgres의 경우는 "
tags: ["S3", "aws", "postgres", "query_export_to_s3", "table_import_from_s3"]
categories: ["Tech Note", "Cloud"]
---
# aws aurora rds가 제공하는 편의 기능
- 업무에서 환경을 나누어서 작업을 하다보면 db간의 데이터 이관 또는 파일로부터의 벌크로드를 해야 하는케이스가 굉장히 많다. 
- 때문에 rdb들은 관련 기능들을 파일로 제공 하고 있으며 동일 목적의 기능으로는 가장 강력한 성능을 주로 보여준다. postgres의 경우는  copy to / copy from 또는 pg_dump / pg_restore 두가지 기능이 가장 많이 사용되고 있다.
- aws rds postgres는 이를 래핑해서 아예 내부에서 익스텐션으로 제공하고 있으며 사용법도 간단하다.
[query_export_to_s3](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/postgresql-s3-export.html)
[table_import_from_s3](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/USER_PostgreSQL.S3Import.html)

# delimiter 설정
- 실제로 업무를 하다보면 텍스트 안에 기존에 사용하단 딜리미터가 들어있는 경우가 많고 파일자체도 내보내다 깨지는 경우가 많이 있다.
- 이로 인해 명령 사용시 quote를 강제로 지정해주는게 좋으며 문법에서 제공하는 copy 옵션들을 충분히 사용하도록 하자
[postgres copy option](https://www.postgresql.org/docs/current/sql-copy.html)
https://www.postgresql.org/docs/current/sql-copy.html

# 딜리미터와 quote 를 강제하는 예시
![사진](/assets/img/posts/24a8ebba-1672-42bc-952c-e240e12e3fb2-image.png)

