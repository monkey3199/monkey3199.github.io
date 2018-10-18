---
title: "[GCloud] Cloud Registry 이미지 Public으로 공개하기"
categories: GCloud, CloudRegistry, public

---

<h1 id="gcr-이미지-public으로-공개-하기">GCR 이미지 Public으로 공개 하기</h1>
<p>Google Container Registry(이하 GCR)에 올려놓은 이미지를 공개적으로 접근할 수 있도록 하려면 해당 프로젝트의 이미지들이 올라가있는 Storage Bucket을 공개적으로 설정해야 한다. 그러므로 해당 프로젝트에 업로드 된 모든 이미지들이 공개가 되니 주의해야 한다.</p>
<h2 id="push-a-image-to-a-registry">Push a image to a registry</h2>
<p>먼저 GCR에 이미지를 올린다.</p>
<h3 id="docker-build">Docker Build</h3>
<p>Docker 명령어를 이용해서 이미지를 빌드한다. 이 때 모든 아규먼트들을 적절히 입력한다.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">export</span> VERSION_TAG<span class="token operator">=</span><span class="token variable"><span class="token variable">$(</span><span class="token function">date</span> +%s<span class="token variable">)</span></span>
<span class="token function">export</span> TRAIN_IMG_PATH<span class="token operator">=</span>us.gcr.io/<span class="token variable">${PROJECT_ID}</span>/kubeflow-train:<span class="token variable">${VERSION_TAG}</span>
docker build -t <span class="token variable">${TRAIN_IMG_PATH}</span> <span class="token variable">${DOCKER_PATH}</span> \ 
	--build-arg version<span class="token operator">=</span><span class="token variable">${VERSION_TAG}</span>
</code></pre>
<h3 id="docker-test-locally">Docker Test locally</h3>
<p>GCR에 이미지를 올리기 전에 미리 테스트를 해본다.</p>
<pre class=" language-bash"><code class="prism  language-bash">docker run -it <span class="token variable">${TRAIN_IMG_PATH}</span>
</code></pre>
<h3 id="allow-docker-to-access-gcr">Allow Docker to access GCR</h3>
<p>Private하게 이미지를 올리고 그 이미지에 접근하기 위해서는 다음과 같이 인증해줘야한다.<br>
Public하게 올려놓을 때는 필요없다.</p>
<pre class=" language-bash"><code class="prism  language-bash">gcloud auth configure-docker
</code></pre>
<h2 id="make-the-storage-bucket-publicly-accessible">Make The Storage Bucket Publicly Accessible</h2>
<ol>
<li>먼저 gcp api를 사용할 수 있는 서버나 Cloud shell에서 버킷의 이름을 검색한다.</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash">gsutil <span class="token function">ls</span>
</code></pre>
<ol start="2">
<li>1번에서 명령어를 사용해서 검색한 리스트 중에 Container Registsry Bucket을 찾는다.  Container Registry Bucket의 URL은 <code>gs://artifacts.[PROJECT-ID].appspot.com</code> or <code>gs://[REGION].artifacts.[PROJECT-ID].appspot.com</code> 와 같은 형태이다.</li>
<li>2번에서 찾은 버킷의 접근 권한을 변경한다.</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash">gsutil iam ch allUsers:objectViewer gs://<span class="token punctuation">[</span>BUCKET_NAME<span class="token punctuation">]</span>
</code></pre>

