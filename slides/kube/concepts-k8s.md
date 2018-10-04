# Kubernetes concepts

- Kubernetes is a container management system

- It runs and manages containerized applications on a cluster

- What does that really mean?

---

## Basic things we can ask Kubernetes to do


- Start 5 containers using image `atseashop/api:v1.3`

- Place an internal load balancer in front of these containers

- Start 10 containers using image `atseashop/webfront:v1.3`

- Place a public load balancer in front of these containers

- It's Black Friday (or Christmas), traffic spikes, grow our cluster and add containers

- New release! Replace my containers with the new image `atseashop/webfront:v1.4`

- Keep processing requests during the upgrade; update my containers one at a time

---

## Other things that Kubernetes can do for us

- Basic autoscaling

- Blue/green deployment, canary deployment

- Long running services, but also batch (one-off) jobs

- Overcommit our cluster and *evict* low-priority jobs

- Run services with *stateful* data (databases etc.)

- Fine-grained access control defining *what* can be done by *whom* on *which* resources

- Integrating third party services (*service catalog*)

- Automating complex tasks (*operators*)

---

## Kubernetes resources

- The Kubernetes API defines a lot of objects called *resources*

- These resources are organized by type, or `Kind` (in the API)

- A few common resource types are:

  - node (a machine — physical or virtual — in our cluster)
  - pod (group of containers running together on a node)
  - service (stable network endpoint to connect to one or multiple containers)
  - namespace (more-or-less isolated group of things)
  - secret (bundle of sensitive data to be passed to a container)
 
  And much more! (We can see the full list by running `kubectl get`) for kubectl v1.10 or `kubectl api-resources` for kubectl 1.11

---

class: pic

![Node, pod, container](images/k8s-arch3-thanks-weave.png)

---

class: pic

![Node, pod, container](images/k8s_training_nodes_and_pods.png)

---

## Credits

- The diagram is courtesy of Weave Works

  - a *pod* can have multiple containers working together

  - IP addresses are associated with *pods*, not with individual containers

Diagram used with permission.
