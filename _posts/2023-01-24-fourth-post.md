# Front Matter
---
title: "비동기 태스크 큐"
categories: tool
tags: jekyll
# 목차
toc: true  
toc_sticky: true 
---

# 대중적인 태스크 큐 소프트웨어
1. celery
- 장점 : backend DB를 설정할 수 있어, redis 말고 따로 저장하지 않아도 된다.


2. redis queue
- 단점 : backend DB 설정이 불가능하여, 만약 영구적인(redis 는 in-memory) 저장이 필요하다면 따로 구축 필요