---
title: "[Kubeflow] Kubeflow란?"
categories: develop AI kubeflow

---

<h1 id="kubeflow">Kubeflow</h1>
<h2 id="kubeflow-1">Kubeflow?</h2>
<p>Kubeflow란 Kubernetes 환경에서 간단하게 머신 러닝 워크플로우를 배포할 수 있게 도와주는 시스템이다. 다양한 환경에서 실행하는 머신 러닝 워크플로우를 단일 환경에서 손쉽고 직관적으로 배포하게 하는 것이 Kubeflow의 목적이다.</p>
<ul>
<li>다양한 인프라 환경에 쉽고 재사용 가능하고 포터블한 배포 가능</li>
<li>loosely-coupled 마이크로서비스 배포 및 관리</li>
<li>요구에 따라 가능한 스케일링</li>
</ul>
<h2 id="kubernetes">Kubernetes?</h2>
<p>Kubernetes는 디플로이 자동화, 스케일링, 컨테이너화된 애플리케이션의 관리를 위한 오픈 소스 시스템으로서 원래 구글에 의해 설계되었고 현재 리눅스 재단에 의해 관리되고 있다. 목적은 여러 클러스터의 호스트 간에 애플리케이션 컨테이너의 배치, 스케일링, 운영을 자동화하기 위한 플랫폼을 제공하기 위함이다. 도커를 포함하여 일련의 컨테이너 도구들과 함께 동작한다.</p>
<p><a href="https://ko.wikipedia.org/wiki/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4">출처: 위키백과 - 쿠버네티스</a></p>
<h2 id="kubeflow-주요-기능">Kubeflow 주요 기능</h2>
<h3 id="ksonnet">Ksonnet</h3>
<p><a href="https://www.kubeflow.org/docs/guides/components/ksonnet/">참고 - Ksonnet</a><br>
Kubeflow는 기본적으로 Ksonnet을 이용해서 서비스를 배포한다. Ksonnet은 kubectl 위에서 작동하며 Kubernetes 환경으로의 배포를 추상화하여 여러 환경으로 배포할 수 있도록 한다. Kubernetes가 배포 정보를 정적인 yaml 파일로 관리하는데, Ksonnet은 프로토타입과 컴포넌트를 이용해서 이 yaml 파일을 동적으로 생성한다. 프로토타입은 Kubernetes에 배포될 자원 명세로 설정 가능한 파라메터를 제외한 형식이다. 컴포넌트는 프로토 타입에 파라메터를 주입한 구현체로 이를 이용해서 다양한 yaml 파일을 생성해서 다양한 환경에 배포를 할 수 있다.<br>
<img src="https://ksonnet.io/docs/img/prototype_and_parameters.svg" alt="출처 - https://ksonnet.io/docs/concepts/"><br>
출처 - <a href="https://ksonnet.io/docs/concepts/">https://ksonnet.io/docs/concepts/</a></p>
<h3 id="tensorflow-training-controller">TensorFlow Training Controller</h3>
<p>Kubeflow에서 TensorFlow 트레이닝 잡을 돌리는 기능으로 TFJob으로 정의한다. TFJob은 쿠버네티스의 custom resource로 만들어져있다. 아래의 예시와 같이 YAML 파일로 정의해서 실행시키며 Distributed Tensorflow의 형태로 배포된다.</p>
<pre class=" language-yaml"><code class="prism  language-yaml"><span class="token key atrule">apiVersion</span><span class="token punctuation">:</span> kubeflow.org/v1alpha2
<span class="token key atrule">kind</span><span class="token punctuation">:</span> TFJob
<span class="token key atrule">metadata</span><span class="token punctuation">:</span>
  <span class="token key atrule">labels</span><span class="token punctuation">:</span>
    <span class="token key atrule">experiment</span><span class="token punctuation">:</span> experiment10
  <span class="token key atrule">name</span><span class="token punctuation">:</span> tfjob
  <span class="token key atrule">namespace</span><span class="token punctuation">:</span> kubeflow
<span class="token key atrule">spec</span><span class="token punctuation">:</span>
  <span class="token key atrule">tfReplicaSpecs</span><span class="token punctuation">:</span>
    <span class="token key atrule">Ps</span><span class="token punctuation">:</span>
      <span class="token key atrule">replicas</span><span class="token punctuation">:</span> <span class="token number">1</span>
      <span class="token key atrule">template</span><span class="token punctuation">:</span>
        <span class="token key atrule">metadata</span><span class="token punctuation">:</span>
          <span class="token key atrule">creationTimestamp</span><span class="token punctuation">:</span> <span class="token null important">null</span>
        <span class="token key atrule">spec</span><span class="token punctuation">:</span>
          <span class="token key atrule">containers</span><span class="token punctuation">:</span>
          <span class="token punctuation">-</span> <span class="token key atrule">args</span><span class="token punctuation">:</span>
            <span class="token punctuation">-</span> python
            <span class="token punctuation">-</span> tf_cnn_benchmarks.py
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>batch_size=32
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>model=resnet50
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>variable_update=parameter_server
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>flush_stdout=true
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>num_gpus=1
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>local_parameter_device=cpu
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>device=cpu
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>data_format=NHWC
            <span class="token key atrule">image</span><span class="token punctuation">:</span> gcr.io/kubeflow/tf<span class="token punctuation">-</span>benchmarks<span class="token punctuation">-</span>cpu<span class="token punctuation">:</span>v20171202<span class="token punctuation">-</span>bdab599<span class="token punctuation">-</span>dirty<span class="token punctuation">-</span>284af3
            <span class="token key atrule">name</span><span class="token punctuation">:</span> tensorflow
            <span class="token key atrule">ports</span><span class="token punctuation">:</span>
            <span class="token punctuation">-</span> <span class="token key atrule">containerPort</span><span class="token punctuation">:</span> <span class="token number">2222</span>
              <span class="token key atrule">name</span><span class="token punctuation">:</span> tfjob<span class="token punctuation">-</span>port
            <span class="token key atrule">resources</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
            <span class="token key atrule">workingDir</span><span class="token punctuation">:</span> /opt/tf<span class="token punctuation">-</span>benchmarks/scripts/tf_cnn_benchmarks
          <span class="token key atrule">restartPolicy</span><span class="token punctuation">:</span> OnFailure
    <span class="token key atrule">Worker</span><span class="token punctuation">:</span>
      <span class="token key atrule">replicas</span><span class="token punctuation">:</span> <span class="token number">1</span>
      <span class="token key atrule">template</span><span class="token punctuation">:</span>
        <span class="token key atrule">metadata</span><span class="token punctuation">:</span>
          <span class="token key atrule">creationTimestamp</span><span class="token punctuation">:</span> <span class="token null important">null</span>
        <span class="token key atrule">spec</span><span class="token punctuation">:</span>
          <span class="token key atrule">containers</span><span class="token punctuation">:</span>
          <span class="token punctuation">-</span> <span class="token key atrule">args</span><span class="token punctuation">:</span>
            <span class="token punctuation">-</span> python
            <span class="token punctuation">-</span> tf_cnn_benchmarks.py
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>batch_size=32
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>model=resnet50
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>variable_update=parameter_server
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>flush_stdout=true
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>num_gpus=1
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>local_parameter_device=cpu
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>device=cpu
            <span class="token punctuation">-</span> <span class="token punctuation">-</span><span class="token punctuation">-</span>data_format=NHWC
            <span class="token key atrule">image</span><span class="token punctuation">:</span> gcr.io/kubeflow/tf<span class="token punctuation">-</span>benchmarks<span class="token punctuation">-</span>cpu<span class="token punctuation">:</span>v20171202<span class="token punctuation">-</span>bdab599<span class="token punctuation">-</span>dirty<span class="token punctuation">-</span>284af3
            <span class="token key atrule">name</span><span class="token punctuation">:</span> tensorflow
            <span class="token key atrule">ports</span><span class="token punctuation">:</span>
            <span class="token punctuation">-</span> <span class="token key atrule">containerPort</span><span class="token punctuation">:</span> <span class="token number">2222</span>
              <span class="token key atrule">name</span><span class="token punctuation">:</span> tfjob<span class="token punctuation">-</span>port
            <span class="token key atrule">resources</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
            <span class="token key atrule">workingDir</span><span class="token punctuation">:</span> /opt/tf<span class="token punctuation">-</span>benchmarks/scripts/tf_cnn_benchmarks
          <span class="token key atrule">restartPolicy</span><span class="token punctuation">:</span> OnFailure
<span class="token key atrule">status</span><span class="token punctuation">:</span>
  <span class="token key atrule">conditions</span><span class="token punctuation">:</span>
  <span class="token punctuation">-</span> <span class="token key atrule">lastTransitionTime</span><span class="token punctuation">:</span> <span class="token datetime number">2018-07-29T00:31:48Z</span>
    <span class="token key atrule">lastUpdateTime</span><span class="token punctuation">:</span> <span class="token datetime number">2018-07-29T00:31:48Z</span>
    <span class="token key atrule">message</span><span class="token punctuation">:</span> TFJob tfjob is running.
    <span class="token key atrule">reason</span><span class="token punctuation">:</span> TFJobRunning
    <span class="token key atrule">status</span><span class="token punctuation">:</span> <span class="token string">"True"</span>
    <span class="token key atrule">type</span><span class="token punctuation">:</span> Running
  <span class="token key atrule">startTime</span><span class="token punctuation">:</span> <span class="token datetime number">2018-07-29T21:40:13Z</span>
  <span class="token key atrule">tfReplicaStatuses</span><span class="token punctuation">:</span>
    <span class="token key atrule">PS</span><span class="token punctuation">:</span>
      <span class="token key atrule">active</span><span class="token punctuation">:</span> <span class="token number">1</span>
    <span class="token key atrule">Worker</span><span class="token punctuation">:</span>
      <span class="token key atrule">active</span><span class="token punctuation">:</span> <span class="token number">1</span>
</code></pre>
<p><a href="https://www.kubeflow.org/docs/guides/components/tftraining/">출처 - Kuberflow 홈페이지</a></p>
<h2 id="tensorflow-serving">TensorFlow Serving</h2>
<p>Kubeflow를 이용해서 TensorFlow Serving을 하기 위해서는 먼저 Http-proxy 컨테이너를 배포해야한다. (이 부분은 더 쉽게 할 수 있도록 지원한다고 한다.)<br>
또한 사용할 수 있는 모델은 Google cloud, Amazon S3, NFS에서 가져올 수 있도록 지원한다.<br>
GPU 설정과 같은 파라메터 설정을 한 후 앱을 배포하면 설정한 URL로 텐서플로우 모델을 서빙할 수 있다.</p>
<h2 id="jupyter-notebooks">Jupyter Notebooks</h2>
<p>Kubeflow를 설치하면 Kubeflow Dashboard가 생성된다. 이곳에서는 텐서플로우 Job에 대한 모니터링 등 간단하게 현재 Kubeflow의 상황을 모니터링 할 수 있다. 또한 Jupyter Hub 가 제공되어 적절한 크기의 컨테이너를 할당받은 Jupyter notebook을 사용할 수 있다.</p>
<h2 id="참고">참고</h2>
<p><a href="https://www.kubeflow.org/docs/about/kubeflow/">Kubeflow</a><br>
<a href="https://ko.wikipedia.org/wiki/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4">위키백과 - 쿠버네티스</a><br>
<a href="https://www.slideshare.net/ChunChuanSu/kubeflow-on-google-kubernetes-engine-94995716">Slideshare - Kubeflow on GKE</a></p>

