# Namespaces

- We cannot have two resources with the same name

  (Or can we...?)

- We cannot have two resources *of the same type* with the same name

  (But it's OK to have a `rng` service, a `rng` deployment, and a `rng` daemon set!)

- We cannot have two resources of the same type with the same name *in the same namespace*

  (But it's OK to have e.g. two `rng` services in different namespaces!)

- In other words: **the tuple *(type, name, namespace)* needs to be unique**

  (In the resource YAML, the type is called `Kind`)

---

## Pre-existing namespaces

- We have three namespaces:

  - `default` (can be for applications)

  - `kube-system` (for the control plane)

  - `kube-public` (contains one secret used for cluster discovery)

- If we deploy differently, we may have different namespaces

  - This is what we do in `Cloud Services`: we segregate tenant deployments using namespaces. 
  
  - Multi-tenanted services are deployed into a `common` namespace.

---

## Using namespaces

- We can pass a `-n` or `--namespace` flag to most `kubectl` commands:
  ```bash
  kubectl -n blue get svc
  ```

- We can also use *contexts*

- A context is a *(user, cluster, namespace)* tuple

- We can manipulate contexts with the `kubectl config` command

---

class: pic

![Node, pod, container](images/k8s_training_namespace.png)
