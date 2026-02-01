---
title: "zscaler 포함 npm으로 gemini cli 설치 오류"
date: 2025-07-23
description: "혹시나 위와 같은 오류를 만난 다면.. 당황하지 말고 npm 로그 확인사내 보안 프로그램의 영향으로 ssl 이슈가 있는 경우에 주로 발생위와 같이 잠깐 ssl 관련 체크를 해제하고 설치 가 가능하다.설치 후 원복"
tags: ["Gemini", "npm", "ssl", "zscaler"]
categories: ["Tech Note", "업무환경"]
---
```
➜  ~ npm install -g @google/gemini-cli
npm error code UNABLE_TO_GET_ISSUER_CERT_LOCALLY
npm error errno UNABLE_TO_GET_ISSUER_CERT_LOCALLY
npm error request to https://registry.npmjs.org/@google%2fgemini-cli failed, reason: unable to get local issuer certificate
```

혹시나 위와 같은 오류를 만난 다면.. 당황하지 말고 npm 로그 확인
사내 보안 프로그램의 영향으로 ssl 이슈가 있는 경우에 주로 발생

```
npm config set strict-ssl false
npm install -g @google/gemini-cli
npm config set strict-ssl true
```
위와 같이 잠깐 ssl 관련 체크를 해제하고 설치 가 가능하다.
설치 후 원복