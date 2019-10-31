---
title: "[Kubernetes] Kubernetes 개념 잡기 - 1"
categories: develop kubernetes
---



## 소개

Kubernetes (이하 k8s - Kubernetes의 약자) 는 구글의 내부 플랫폼인 Borg를 **오픈 소스**화 한 **컨테이너** **오케스트레이션** **플랫폼**이다. 하지만 아무리 구글이 만들었고, 많은 기능을 가진 k8s이지만 많은 기능을 가진만큼 익숙해지기까지는 오랜 시간이 필요하다. 이 글에서는 k8s를 어디에 쓰는지, 아주 간단하게 어떤 기능을 가지고 있는지 살펴보겠다.



## Kubernetes?

k8s는 오픈 소스 컨테이너 오케스트레이션 플랫폼이다. 오픈 소스, 컨테이너, 오케스트레이션, 플랫폼까지 모든 단어가 파고들면 다시 또 엄청난 양이 나오는 어려운 말들이다. 그 모두를 적당히 이해하고 있어야 k8s를 사용할 수 있으니 진입장벽이 꽤 높다고 생각한다. 간단히 개념만 짚고 넘어가자.

- 오픈 소스(Open source): 소프트웨어 혹은 하드웨어의 제작자의 권리를 지키면서 원시 코드를 누구나 열람할 수 있도록 한 소프트웨어 혹은 **오픈 소스** 라이선스에 준하는 모든 통칭을 일컫는다. 

- 컨테이너(Container): 운영체제 커널을 공유하는 작고 가벼운 **실행 환경**으로 **분리**된 단위이다. 우리가 흔히 아는 **도커**는 이러한 컨테이너 안에 리눅스 응용 프로그램들의 배치를 **자동화**해주는 도구이다.

- 오케스트레이션(Ochestration): 여기서 말하는 오케스트레이션은 **컨테이너 오케스트레이션**을 말하며, 컨테이너들을 **배포**하고 **관리**하는 것을 의미한다.

  ​																			(참고 - 위키백과)

애플리케이션 배포에 컨테이너를 활용하기 시작하면서, 그 자체에 대한 배포나 관리 등을 자동화 하는 것은 당연한 일이었다. 단순히 하나의 컨테이너를 배포하기 위해서도 네트워크를 구성해야 하고, 컨테이너가 죽었을 때 다시 실행해줘야 하는 등 많은 관리적 어려움이 있었기 때문이다. 즉, k8s는 이러한 **관리적** 차원에서 **컨테이너**들의 배포를 관리하고 자원을 스케줄링하는 등의 기능을 제공하는 **자동화** 플랫폼이라고 할 수 있다. 



## Kubernetes 튜토리얼

k8s 공식 사이트는 현재 한국어로도 활발히 번역 중이고 많은 문서를 제공하여 이해를 돕는다. 특히 Katacode 를 이용해서 웹 환경에서 대화형 튜토리얼을 진행할 수 있어 쉽게 k8s 를 경험해볼 수 있다. 다음 [튜토리얼](https://kubernetes.io/ko/docs/tutorials/hello-minikube/)은 싱글 노드에서 간단하게 실행하는 k8s 구현체인 minikube 를 이용해서 어플리케이션을 배포해보는 예제이다. 이외에도 많은 튜토리얼이 있으니 환경 설정없이도 맘껏 k8s 를 경험해보자.



오늘은 간단히 k8s 가 무엇인지 훑어보았다. 다음 포스팅에서는 k8s 를 이해하기 위해 여러 오브젝트들에 대해서 더 깊게 알아보도록 하겠다.



## 참고

[쿠버네티스란?](https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/)

[쿠버네티스_공식사이트](https://kubernetes.io/ko/)