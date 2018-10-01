---


---

<h1 id="getting-started-with-kubeflow">Getting Started with Kubeflow</h1>
<h2 id="참고">참고</h2>
<p><a href="https://www.kubeflow.org/docs/started/getting-started/">Getting Started with Kubeflow</a></p>
<h2 id="set-up-kubernetes-with-minikube">Set up Kubernetes with Minikube</h2>
<h2 id="kubeflow-quick-start">Kubeflow quick start</h2>
<p>Requirements:</p>
<ul>
<li>ksonnet version  <a href="https://ksonnet.io/#get-started">0.11.0</a>  or later.</li>
<li>Kubernetes  <a href="https://kubernetes.io/docs/imported/release/notes/">1.8</a>  or later</li>
<li>kubectl</li>
</ul>
<h3 id="download">Download</h3>
<pre class=" language-shell"><code class="prism  language-shell">mkdir ${KUBEFLOW_SRC}
cd ${KUBEFLOW_SRC}
export KUBEFLOW_TAG=&lt;version&gt;
curl https://raw.githubusercontent.com/kubeflow/kubeflow/${KUBEFLOW_TAG}/scripts/download.sh | bash
</code></pre>
<blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote>

