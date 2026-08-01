## K8S Concepts

### control plane
It is a brain. It includes:
- etcd: cluster state db -- source of true
- kube-apiserver: cluster front door -- talk with external world
- k8s-scheduler: choose node -- find home for pods
- k8s-contoller-manager: maintain target state -- self-healing engine

### worker node
It is muscle. including:
- kubelet: manager on each worker node
- container runtime: chef who cooks, it starts containers
- kube-proxy: pod networking
- pod: rental apartment, tenant is your app docker image.
    - pod is replaceable, do not rely on pod IP or local filesystem. We do not store state on pods, instead we store them on s3, rds, kafka etc.

### service
Service is a permanent phone number, people use that number could change. Service provide stable endpoint while pod ip changes, it is like a load balancer.

Take an example, our spark driver pod hosted the spark UI on port 4040, but driver pods are temp and the IP changes whenever new pod created. Instead, we create a k8s service always pointing to current driver pod.

### deployment vs statefulSet
- Deployment is for interchangeable stateless replicas, good for a long-run app such as kafka consumer.
- use statefulSet for stable identity with ordered life cycle, persistent volume binding.
    - instead of creating pods, create specific pods such as kafka broker2 rather than spark executor.

### liveness vs readiness
- readiness make sure the app is fully initialized and ready to use.
- liveness detect situations like deadlock, infinite loop and restart container. we avoid making liveness depends on temp kafka availability which will cause unnecessary restarts.

### request vs limit
- request: hotel use it to decide if it can accept your reservation.
    - request is used by the scheduler to decide where driver and exector could run, such as 1G CPU 2G memory
    - use memoryOverheadFactor, pod memory request = memory * (1+memoryOverheadFactor) this fix alert like the pod is using 90% memory.
- limit: even if more rooms empty, hotel will not let you stay
    - limit controls runtime usage. Exceeding CPU limit could result a container throttle, while exceeding memory limit could result container OOMKilled. 
    - we also config spark overhead because container require memory beyond JVM heap for native memory, serialization and memory overhead.

