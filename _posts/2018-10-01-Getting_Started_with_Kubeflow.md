---
title: "[Kubeflow] 설치 및 시작"
categories: develop AI kubeflow

---

<h1 id="getting-started-with-kubeflow">Getting Started with Kubeflow</h1>
<p>Kubeflow란 쿠버네티스 상에서 쉽게 머신러닝을 배포할 수 있는 도구이다.<br>
쿠버네티스 배포는 Minikube를 이용하고, Kubeflow를 테스트하는 환경은 다음과 같다.</p>
<ul>
<li>Mac Os High Sierra</li>
<li>Docker version 18.03.1-ce, build 9ee9f40</li>
</ul>
<h2 id="참고">참고</h2>
<p><a href="https://www.kubeflow.org/docs/started/getting-started/">Getting Started with Kubeflow</a><br>
<a href="https://www.kubeflow.org/docs/started/getting-started-minikube/">Minikube for Kubeflow</a><br>
<a href="https://www.katacoda.com/kubeflow/scenarios/deploying-kubeflow">Katacoda - Deploying Kubeflow</a></p>
<h2 id="set-up-kubernetes-with-minikube">Set up Kubernetes with Minikube</h2>
<h3 id="install-a-hypervisor">Install a Hypervisor</h3>
<p><a href="https://www.virtualbox.org/wiki/Downloads">Virtual Box</a>  이나  <a href="https://www.vmware.com/products/fusion">VMware Fusion</a> 을 설치한다.</p>
<h3 id="install-kubectl">Install Kubectl</h3>
<pre><code>$ brew install kubectl
</code></pre>
<h3 id="install--start-minikube">Install &amp; Start Minikube</h3>
<pre><code>$ brew cask install minikube
</code></pre>
<h3 id="start-your-minikube-cluster">Start your minikube cluster</h3>
<pre><code>$ minikube start --cpus 4 --memory 8096 --disk-size=40g
</code></pre>
<p>그 외 minikube를 컨트롤 할 수 있는 명령어는 다음과 같다.</p>
<pre><code>$ minikube stop
$ minikube delete
</code></pre>
<p>minikube가 정상적으로 실행이 되면 ssh 로 접속을 해보거나, 대쉬보드를 열어서 웹용 브라우져 화면으로 확인할 수 있다.</p>
<pre><code>$ minikube ssh
$ minikube dashboard
</code></pre>
<h2 id="kubeflow-quick-start">Kubeflow quick start</h2>
<p>Requirements:</p>
<ul>
<li>ksonnet version  <a href="https://ksonnet.io/#get-started">0.11.0</a>  or later.</li>
<li>Kubernetes  <a href="https://kubernetes.io/docs/imported/release/notes/">1.8</a>  or later</li>
<li>kubectl</li>
</ul>
<h3 id="install-script">Install script</h3>
<pre><code>$ export GITHUB_TOKEN=99510f2ccf40e496d1e97dbec9f31cb16770b884
$ export KUBEFLOW_VERSION=0.2.5
$ curl https://raw.githubusercontent.com/kubeflow/kubeflow/v${KUBEFLOW_VERSION}/scripts/deploy.sh | bash
</code></pre>
<h3 id="port-forwarding">Port-forwarding</h3>
<pre><code>$ export NAMESPACE=default
$ export POD=`kubectl -n ${NAMESPACE} get pods --selector=service=ambassador | awk '{print $1}' | tail -1`
$ kubectl -n ${NAMESPACE} port-forward $POD 8080:80 2&gt;&amp;1 &gt;/dev/null &amp;
</code></pre>
<h3 id="ambassador-화면-접속">Ambassador 화면 접속</h3>
<p><a href="http://localhost:8080">http://localhost:8080</a></p>

