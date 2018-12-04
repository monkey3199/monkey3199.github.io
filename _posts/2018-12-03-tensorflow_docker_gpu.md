---
title: "[TensorFlow] 텐서플로우 도커에서 GPU 사용하기"
categories: develop AI tensorflow

---

<h1 id="도커에서-gpu-사용하기">도커에서 GPU 사용하기</h1>
<p>도커에서 GPU(vidia)를 사용하기 위해서 nvidia-docker를 설치한다. 설치 환경은 다음과 같다. CUDA와 cuDNN은 TensorFlow에서 GPU 를 사용하기 위한 필수 설치 요소이며, GPU에 따라 지원되는 버전이 상이하니 <a href="https://www.tensorflow.org/install/gpu">TensorFlow 홈페이지</a> 를 참고하자.</p>
<ul>
<li>CentOS 7</li>
<li>Docker version 18.09.0</li>
<li>GeForce GTX950</li>
<li>CUDA 9.0.176.4</li>
<li>cuDNN 7.3.0</li>
</ul>
<p>여기까지 설치했으면 nvidia-smi 명령을 통해서 GPU를 모니터링 할 수 있다.<br>
하지만 도커 컨테이너를 실행하고 그 안에서는 아직 GPU를 사용할 수 없다.</p>
<h2 id="nvidia-docker-설치하기">nvidia-docker 설치하기</h2>
<p>도커 컨테이너에서 GPU를 사용하기 위해서는 nvidia-docker를 설치해야 한다.<br>
<a href="https://github.com/NVIDIA/nvidia-docker">nvidia-docer github</a> 에 자세한 설치 방법이 있다. OS나 도커 버전에 따라 설치 방법이 상이하니 꼭 참고해서 설치하자. 해당 글에서는 위에 설명한대로 CentOS 7, docker-ce 버전이기 때문에 nvidia-docker 2 를 설치했다.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment"># If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers</span>
docker volume <span class="token function">ls</span> -q -f driver<span class="token operator">=</span>nvidia-docker <span class="token operator">|</span> <span class="token function">xargs</span> -r -I<span class="token punctuation">{</span><span class="token punctuation">}</span> -n1 docker <span class="token function">ps</span> -q -a -f volume<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token operator">|</span> <span class="token function">xargs</span> -r docker <span class="token function">rm</span> -f
<span class="token function">sudo</span> yum remove nvidia-docker

<span class="token comment"># Add the package repositories</span>
distribution<span class="token operator">=</span><span class="token variable"><span class="token variable">$(</span><span class="token keyword">.</span> /etc/os-release<span class="token punctuation">;</span><span class="token keyword">echo</span> $ID$VERSION_ID<span class="token variable">)</span></span>
curl -s -L https://nvidia.github.io/nvidia-docker/<span class="token variable">$distribution</span>/nvidia-docker.repo <span class="token operator">|</span> \
  <span class="token function">sudo</span> <span class="token function">tee</span> /etc/yum.repos.d/nvidia-docker.repo

<span class="token comment"># Install nvidia-docker2 and reload the Docker daemon configuration</span>
<span class="token function">sudo</span> yum <span class="token function">install</span> -y nvidia-docker2
<span class="token function">sudo</span> <span class="token function">pkill</span> -SIGHUP dockerd
</code></pre>
<h2 id="gpu-동작-테스트">GPU 동작 테스트</h2>
<p>CentOS 7 에서는 SELinux 모드가 켜져있으면 권한 문제가 발생할 수 있어 따로 설정이 필요하다. 또한 GPU에 따라 사용할 수 있는 CUDA와 cuDNN 버전이 다르니 확인하고 도커 이미지를 사용하자. 또한 --runtime 옵션으로 nvidia를 지정해줘야한다. 혹은 docker 명령어 대신 nvidia-docker 명령으로 실행할 수도 있다.</p>
<pre><code># set SELinux mode
setenforce Permissive
# Test nvidia-smi with the latest official CUDA image
docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
</code></pre>
<h2 id="tensorflow-실행">TensorFlow 실행</h2>
<p>nvidia/cuda 도커는 말그대로 nvidia gpu 드라이버가 설치되어 도커에서 gpu를 사용할 수 있게해주는 기능만 할 뿐 TensorFlow를 실행하기 위해서는 nvidia/cuda 도커에서 TensorFlow를 따로 설치하거나, TensorFlow-GPU 도커 이미지를 이용해야 한다. <a href="https://hub.docker.com/r/tensorflow/tensorflow/tags/">Docker Hub</a> 를 보고 적절한 태그의 이미지를 사용하자.</p>
<pre><code>docker run --runtime=nvidia -it tensorflow/tensorflow:latest-devel-gpu python -c "import tensorflow as tf; tf.Session()"
</code></pre>
<p>다음처럼 TensorFlow 세션을 생성하면 GPU가 인식되는지 확인해볼 수 있다. 환경마다 내용이 다를 수 있으며 현재 환경에서는 다음과 같은 내용을 출력했다.</p>
<pre><code>name: GeForce GTX 950 major: 5 minor: 2 memoryClockRate(GHz): 1.2785
pciBusID: 0000:01:00.0
totalMemory: 1.95GiB freeMemory: 1.91GiB
2018-12-04 04:13:17.463542: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1511] Adding visible gpu devices: 0
2018-12-04 04:13:17.738679: I tensorflow/core/common_runtime/gpu/gpu_device.cc:982] Device interconnect StreamExecutor with strength 1 edge matrix:
2018-12-04 04:13:17.738728: I tensorflow/core/common_runtime/gpu/gpu_device.cc:988]      0
2018-12-04 04:13:17.738741: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1001] 0:   N
2018-12-04 04:13:17.738882: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1115] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 1664 MB memory) -&gt; physical GPU (device: 0, name: GeForce GTX 950, pci bus id: 0000:01:00.0, compute capability: 5.2)
</code></pre>
<p>참고: <a href="https://github.com/NVIDIA/nvidia-docker">nvidia-docker github</a></p>

