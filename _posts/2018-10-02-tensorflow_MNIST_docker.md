---
title: "[Tensorflow] MNIST in docker"
categories: tensorflow, docker, mnist

---

<h1 id="tensorflow-examplemnist-in-docker">Tensorflow Example(MNIST) in Docker</h1>
<p>Docker 를 이용해서 Tensorflow 환경을 생성하고, 간단하게 MNIST 예제를 실행한다.</p>
<h2 id="mnist">MNIST</h2>
<p>MNIST는 0부터 9까지의 손글씨 숫자 이미지 데이터베이스를 의미하며, 해당 이미지를 이용해서 숫자를 예측하는 모델을 Tensorflow를 사용해서 구현한다.<br>
모델 소스코드는 <a href="https://github.com/hunkim/DeepLearningZeroToAll">링크</a>를 참고했다.<br>
해당 예제에서는 주피터 노트북이 아닌 python cli를 이용할 것이므로 matplotlib.pyplot(GUI 라이브러리) 관련 코드는 삭제하도록 한다.</p>
<pre><code># import matplotlib.pyplot as plt
...중략
# plt.imshow(
# mnist.test.images[r:r + 1].reshape(28, 28),
# cmap='Greys',
# interpolation='nearest')
# plt.show()
</code></pre>
<h2 id="dockerfile">Dockerfile</h2>
<p>도커 이미지는 텐서플로우 공식 홈페이지에서 제공하는 기본 이미지를 사용한다. 기본적으로 tensorflow와 jupyter notebook, python2를 제공한다.</p>
<pre><code>FROM tensorflow/tensorflow:1.8.0
RUN mkdir -p /opt/mnist
COPY mnist.py /opt/mnist/
ENTRYPOINT ["python", "/opt/mnist/mnist.py"]
</code></pre>
<h2 id="docker-build--run">Docker Build &amp; Run</h2>
<h3 id="build">Build</h3>
<pre><code>$ docker build -t $(IMG):$(TAG) .
</code></pre>
<h3 id="run">Run</h3>
<pre><code>$ docker run -it $(IMG):$(TAG) .
</code></pre>
<h2 id="결과">결과</h2>
<p>해당 도커를 실행하면 다음과 같이 트레이닝을 거친 뒤에 약 89%의 정확도를 가지는 것을 확인할 수 있다.</p>
<pre><code>('Epoch:', '0001', 'cost =', '2.826302761')
('Epoch:', '0002', 'cost =', '1.061668983')
('Epoch:', '0003', 'cost =', '0.838061320')
('Epoch:', '0004', 'cost =', '0.733232755')
('Epoch:', '0005', 'cost =', '0.669279891')
('Epoch:', '0006', 'cost =', '0.624611840')
('Epoch:', '0007', 'cost =', '0.591160361')
('Epoch:', '0008', 'cost =', '0.563868995')
('Epoch:', '0009', 'cost =', '0.541745183')
('Epoch:', '0010', 'cost =', '0.522673592')
('Epoch:', '0011', 'cost =', '0.506782337')
('Epoch:', '0012', 'cost =', '0.492447647')
('Epoch:', '0013', 'cost =', '0.479955846')
('Epoch:', '0014', 'cost =', '0.468893675')
('Epoch:', '0015', 'cost =', '0.458703481')
Learning finished
('Accuracy: ', 0.8951)
('Label: ', array([6]))
('Prediction: ', array([6]))
</code></pre>

