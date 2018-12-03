---
extensions:
  preset: ''

---

<h1 id="도커에서-gpu-사용하기">도커에서 GPU 사용하기</h1>
<h2 id="nvidia-docker-설치하기">nvidia-docker 설치하기</h2>
<p>도커에서 GPU(nvidia)를 사용하기 위해서 nvidia-docker를 설치한다. 설치 환경은 다음과 같다.</p>
<ul>
<li>CentOS 7</li>
<li>Docker 1.13.1</li>
<li>GeForce GTX650</li>
</ul>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment"># If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers</span>
docker volume <span class="token function">ls</span> -q -f driver<span class="token operator">=</span>nvidia-docker <span class="token operator">|</span> <span class="token function">xargs</span> -r -I<span class="token punctuation">{</span><span class="token punctuation">}</span> -n1 docker <span class="token function">ps</span> -q -a -f volume<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token operator">|</span> <span class="token function">xargs</span> -r docker <span class="token function">rm</span> -f
<span class="token function">sudo</span> yum remove nvidia-docker

<span class="token comment"># Add the package repositories</span>
distribution<span class="token operator">=</span><span class="token variable"><span class="token variable">$(</span><span class="token keyword">.</span> /etc/os-release<span class="token punctuation">;</span><span class="token keyword">echo</span> $ID$VERSION_ID<span class="token variable">)</span></span>
curl -s -L https://nvidia.github.io/nvidia-container-runtime/<span class="token variable">$distribution</span>/nvidia-container-runtime.repo <span class="token operator">|</span> \
  <span class="token function">sudo</span> <span class="token function">tee</span> /etc/yum.repos.d/nvidia-container-runtime.repo

<span class="token comment"># Install the nvidia runtime hook</span>
<span class="token function">sudo</span> yum <span class="token function">install</span> -y nvidia-container-runtime-hook
</code></pre>
<h2 id="gpu-동작-테스트">GPU 동작 테스트</h2>
<p>CentOS 7 에서는 SELinux 모드가 켜져있으면 권한 문제가 발생할 수 있어 따로 설정이 필요하다. 또한 GPU에 따라 사용할 수 있는 CUDA와 cuDNN 버전이 다르니 확인하고 도커 이미지를 사용하자.</p>
<pre><code># set SELinux mode
setenforce Permissive
# Test nvidia-smi with the latest official CUDA image
# You can't use `--runtime=nvidia` with this setup.
docker run --it nvidia/cuda:8.0-cudnn5-runtime sh -c "nvidia-smi"
</code></pre>
<h2 id="tensorflow-실행">TensorFlow 실행</h2>
<p>nvidia/cuda 도커는 말그대로 nvidia gpu 드라이버가 설치되어 도커에서 gpu를 사용할 수 있게해주는 기능만 할 뿐 텐서플로우를 실행하기 위해서는 따로 설치를 해줘야한다.<br>
python 사용을 위해 nvidia/cuda 도커 centos7 버전의 이미지를 사용한다. 해당 도커 이미지를 이용해서 텐서플로우를 설치하고 원하는 python 코드를 실행하는 Dockerfile은 다음과 같다</p>
<pre class=" language-docker"><code class="prism  language-docker"><span class="token keyword">FROM</span> docker.io/nvidia/cuda<span class="token punctuation">:</span>8.0<span class="token punctuation">-</span>cudnn5<span class="token punctuation">-</span>devel<span class="token punctuation">-</span>centos7
<span class="token comment"># install pip and tensorflow</span>
<span class="token keyword">RUN</span> yum <span class="token punctuation">-</span>y install epel<span class="token punctuation">-</span>release
<span class="token keyword">RUN</span> yum <span class="token punctuation">-</span>y install python<span class="token punctuation">-</span>pip
<span class="token keyword">RUN</span> pip install tensorflow_gpu==1.0.0
<span class="token comment"># copy to run python code</span>
<span class="token keyword">RUN</span> mkdir <span class="token punctuation">-</span>p /opt/mnist
<span class="token keyword">COPY</span> mnist_cnn.py /opt/mnist/
<span class="token keyword">ENTRYPOINT</span> <span class="token punctuation">[</span><span class="token string">"python"</span><span class="token punctuation">,</span> <span class="token string">"/opt/mnist/mnist_cnn.py"</span><span class="token punctuation">]</span>
</code></pre>
<p>참고: <a href="https://github.com/NVIDIA/nvidia-docker">nvidia-docker github</a></p>

