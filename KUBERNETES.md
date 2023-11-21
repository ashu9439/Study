Problem :
- Containers are Ephemeral(short life / die and revived easily)
- Scope - single host 
- No auto healing - if container dies, cant revived by its own
- No auto Scaling - can not automatically spin up a new instance acording to load.
- Not enterprise ready :
    - no load balancer
    - No firewall
    - No auto scaling
    - No auto healing
    - No API gateway

*****
# K8s :
  - cluster - group of nodes - 
  - Master-Slave architecture
  - auto healing by replication controller and horizontal pod autoscaler(hpa)
      - api server creates a new pod before a pod goes down

*****
# Architecture :
Control Plane :
    Api server
    ETCD
    Scheduler
    Controller manager
    ccm ( cloud controller manager)
data Plane :
    kubelets
    kube proxy
    container runtime

![image](https://github.com/ashu9439/Study/assets/46566670/f8f2efae-d998-427e-922d-302de377afc0)

## Worker node components: / data plane
 - Kubelet
        responsible for creation of pods and keepimng t in running state 
 - kube-Proxy
        uses ip table for networking 
 - Container run time
       running the container

## Master node components: / controller plane
- Api server
- Scheduler
- etcd
- controller manager
- ccm

# practicle :
 1. install miniKube
 2. install Kubectl

****

# list of Kubernates distibutions according to popularity
1. Kubernaetes on EC2
2. openshift
3. rancher
4. tanzu
5. EKS
6. Aks

*****
# tools to manage clusters on production
1. kops
2. kubeadm
   
    
    
    
    
