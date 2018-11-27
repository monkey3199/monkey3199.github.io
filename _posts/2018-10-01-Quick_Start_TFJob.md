---
title: "[Kubeflow] TFJob Quick Start"
categories: develop AI kubeflow

---

<h1 id="quick-start-tensorflow-application">Quick Start TensorFlow Application</h1>
<h2 id="출처">출처</h2>
<p><a href="https://www.katacoda.com/kubeflow/scenarios/deploying-kubeflow">Katacoda - Deploying Kubeflow</a></p>
<h2 id="example-tensorflow-application">Example TensorFlow Application</h2>
<p><a href="https://github.com/tensorflow/k8s/tree/master/examples/tf_sample">코드 출처(https://github.com/tensorflow/k8s/tree/master/examples/tf_sample)</a></p>
<pre class=" language-python"><code class="prism  language-python"><span class="token keyword">for</span> job_name <span class="token keyword">in</span> cluster_spec<span class="token punctuation">.</span>keys<span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">:</span>
  <span class="token keyword">for</span> i <span class="token keyword">in</span> <span class="token builtin">range</span><span class="token punctuation">(</span><span class="token builtin">len</span><span class="token punctuation">(</span>cluster_spec<span class="token punctuation">[</span>job_name<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">:</span>
    d <span class="token operator">=</span> <span class="token string">"/job:{0}/task:{1}"</span><span class="token punctuation">.</span><span class="token builtin">format</span><span class="token punctuation">(</span>job_name<span class="token punctuation">,</span> i<span class="token punctuation">)</span>
    <span class="token keyword">with</span> tf<span class="token punctuation">.</span>device<span class="token punctuation">(</span>d<span class="token punctuation">)</span><span class="token punctuation">:</span>
      a <span class="token operator">=</span> tf<span class="token punctuation">.</span>constant<span class="token punctuation">(</span><span class="token builtin">range</span><span class="token punctuation">(</span>width <span class="token operator">*</span> height<span class="token punctuation">)</span><span class="token punctuation">,</span> shape<span class="token operator">=</span><span class="token punctuation">[</span>height<span class="token punctuation">,</span> width<span class="token punctuation">]</span><span class="token punctuation">)</span>
      b <span class="token operator">=</span> tf<span class="token punctuation">.</span>constant<span class="token punctuation">(</span><span class="token builtin">range</span><span class="token punctuation">(</span>width <span class="token operator">*</span> height<span class="token punctuation">)</span><span class="token punctuation">,</span> shape<span class="token operator">=</span><span class="token punctuation">[</span>height<span class="token punctuation">,</span> width<span class="token punctuation">]</span><span class="token punctuation">)</span>
      c <span class="token operator">=</span> tf<span class="token punctuation">.</span>multiply<span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span>
      results<span class="token punctuation">.</span>append<span class="token punctuation">(</span>c<span class="token punctuation">)</span>
</code></pre>
<h2 id="tfjob-definition">TFJob Definition</h2>
<p>example.yaml 명세를 정의한다.</p>
<pre class=" language-yaml"><code class="prism  language-yaml"><span class="token key atrule">apiVersion</span><span class="token punctuation">:</span> <span class="token string">"kubeflow.org/v1alpha2"</span>
<span class="token key atrule">kind</span><span class="token punctuation">:</span> <span class="token string">"TFJob"</span>
<span class="token key atrule">metadata</span><span class="token punctuation">:</span>
	<span class="token key atrule">name</span><span class="token punctuation">:</span> <span class="token string">"example-job"</span>
<span class="token key atrule">spec</span><span class="token punctuation">:</span>
	<span class="token key atrule">tfReplicaSpecs</span><span class="token punctuation">:</span>
		<span class="token key atrule">Master</span><span class="token punctuation">:</span>
			<span class="token key atrule">replicas</span><span class="token punctuation">:</span> <span class="token number">1</span>
			<span class="token key atrule">restartPolicy</span><span class="token punctuation">:</span> Never
			<span class="token key atrule">template</span><span class="token punctuation">:</span>
				<span class="token key atrule">spec</span><span class="token punctuation">:</span>
					<span class="token key atrule">containers</span><span class="token punctuation">:</span>
						<span class="token punctuation">-</span> <span class="token key atrule">name</span><span class="token punctuation">:</span> tensorflow
						  <span class="token key atrule">image</span><span class="token punctuation">:</span> gcr.io/tf<span class="token punctuation">-</span>on<span class="token punctuation">-</span>k8s<span class="token punctuation">-</span>dogfood/tf_sample<span class="token punctuation">:</span>dc944ff
		<span class="token key atrule">Worker</span><span class="token punctuation">:</span>
			<span class="token key atrule">replicas</span><span class="token punctuation">:</span> <span class="token number">1</span>
			<span class="token key atrule">restartPolicy</span><span class="token punctuation">:</span> Never
			<span class="token key atrule">template</span><span class="token punctuation">:</span>
				<span class="token key atrule">spec</span><span class="token punctuation">:</span>
					<span class="token key atrule">containers</span><span class="token punctuation">:</span>
						<span class="token punctuation">-</span> <span class="token key atrule">name</span><span class="token punctuation">:</span> tensorflow
						  <span class="token key atrule">image</span><span class="token punctuation">:</span> gcr.io/tf<span class="token punctuation">-</span>on<span class="token punctuation">-</span>k8s<span class="token punctuation">-</span>dogfood/tf_sample<span class="token punctuation">:</span>dc944ff
		<span class="token key atrule">PS</span><span class="token punctuation">:</span>
			<span class="token key atrule">replicas</span><span class="token punctuation">:</span> <span class="token number">2</span>
			<span class="token key atrule">restartPolicy</span><span class="token punctuation">:</span> Never
			<span class="token key atrule">template</span><span class="token punctuation">:</span>
				<span class="token key atrule">spec</span><span class="token punctuation">:</span>
					<span class="token key atrule">containers</span><span class="token punctuation">:</span>
						<span class="token punctuation">-</span> <span class="token key atrule">name</span><span class="token punctuation">:</span> tensorflow
						  <span class="token key atrule">image</span><span class="token punctuation">:</span> gcr.io/tf<span class="token punctuation">-</span>on<span class="token punctuation">-</span>k8s<span class="token punctuation">-</span>dogfood/tf_sample<span class="token punctuation">:</span>dc944ff
</code></pre>
<h2 id="deploying-tfjob">Deploying TFJob</h2>
<pre><code>$ kubectl apply -f example.yaml
</code></pre>
<h2 id="job-결과-보기">Job 결과 보기</h2>
<pre><code>$ kubectl get tfjob
$ kubectl get pods | grep Completed
</code></pre>
<p>다음 명령어를 통해 로그를 확인하면 tfjob이 실행된 과정을 살펴볼 수 있다.</p>
<pre><code>kubectl logs $(kubectl get pods | grep Completed | tr -s ' ' | cut -d ' ' -f 1)
</code></pre>

