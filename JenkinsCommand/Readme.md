jenkins command
===


https://waspro.tistory.com/554

1. Jenkins 파이프라인 정의

Jenkins 파이프라인은 여러 방식으로 구현이 가능합니다.

- Jenkins Webadmin : 일반적인 방식으로 Jenkins 파이프라인을 생성하여 Shell Script를 직접 생성하여 빌드하는 방식

- Git SCM : Git Repository에 JenkinsFile을 작성하여 빌드하는 방식

- Blue Ocean : 파이프라인을 시각화하여 손쉽게 구성하여 빌드하는 방식

2. Jenkins 파이프라인 Script 문법 정의


2. Jenkins 파이프라인 Script 문법 정의

1) Pipeline

```
pipeline {
    /* insert Declarative Pipeline here */
}
```
- 파이프라인을 정의하기 위해서는 반드시 pipeline을 포함해야 합니다.

- pipeline은 최상위 레벨이 되어야하며, { }으로 정의해야 합니다.

- pipeline은 Section, Directives, Steps 또는 assignment 문으로만 구성되야 합니다.
