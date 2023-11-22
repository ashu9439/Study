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
<img width="275" alt="image" src="https://github.com/ashu9439/Study/assets/46566670/e4aa1bcd-626e-4605-925b-9f8a0f1dfb63">

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
 1. install miniKube(dev env k8s cluster)
 2. install Kubectl( cmd ln i/f)

****
local k8s cluster :
    - minikube
    - k3s
    - kind (prefer)
    - microk8s
    

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
****

# Pods :
-	Definition of how to run a container
-	Single / multiple container
-	.yaml 
-	Multiple container ( shared Network, shared Storage)


****
# Kube-ctl 
 - commandline interface for k8s


*****
# K8s deployment
    - helps to do 
        - auto healing 
        - auto scaling

difference between ?
container <--> pod   <--> deployment 

deplyment resource -> replica set(k8s controller) -> pods
   
    
# k8s controllers :
- helps to keep the actual state of the cluster same as the desired state(written in the deployemt .yaml file)
- helps to do uto healing and auto scaling
- 
default: replica set
custom : argo cd, etc

*****

create deployemnt 
    -> deployment will create replica set 
        -> replica set will manage pod (auto heaing : if a pod will go down. deleted then it will create/ or help to keep desired no of pods running always
        

# k8s services :
- helps to act as a *load balancer* (with help of Kube proxy)
- service discovery :
      - by keeping *labels of pods & selectors*
- help to expose the cluster(application/ pods) to external world


So in summary we can create a service in the following types:
    1. cluster ip 
        - helps in discovery and load belancing 
        - if you want the application is only accessible to devops / developers , then create service as cluster ip
    2. node port
        allow the nodes to be accessed inside orgnization ( so anyone have access to workernode ip address can use application)
        - if application need to be accessible people in side the organization/ vpc like developer/tester
    3. Load balancer
        - expose the application to external world
        - if application need to be accessible  general people






<img width="596" alt="image" src="https://github.com/ashu9439/Study/assets/46566670/aef1c6f4-298f-4967-8ccc-161ad2498693">

<img width="608" alt="image" src="https://github.com/ashu9439/Study/assets/46566670/e2f3c163-78d0-4ca0-ac36-10e0808b1b07">

<img width="473" alt="image" src="https://github.com/ashu9439/Study/assets/46566670/1a77e7d6-9679-49b7-805a-c5f8719acae0">

<img width="536" alt="image" src="https://github.com/ashu9439/Study/assets/46566670/86d1582f-a42e-43be-8227-d50bbbfe470c">

<img width="315" alt="image" src="https://github.com/ashu9439/Study/assets/46566670/7fbf9660-689f-4539-81c5-aa55c8bd0bb4">




