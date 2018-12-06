# GKE 에서 GPU 사용하기



## GKE GPU 클러스터 디플로이



## Nvidia drive 설치

```bash
kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/stable/nvidia-driver-installer/cos/daemonset-preloaded.yaml
```



## nvidia-smi 테스트

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-gpu-pod
spec:
  containers:
  - name: my-gpu-container
    image: nvidia/cuda:9.0-runtime-ubuntu16.04
    command: ["sleep", "10000000"]
    resources:
      limits:
       nvidia.com/gpu: 1
```

