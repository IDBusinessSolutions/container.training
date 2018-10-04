# Daemon sets

- What if we want to scale `rng` in a way that is different from how we scaled `worker`

- For example, what if we want one (and exactly one) instance of `rng` per node

- What if we just scale up `deploy/rng` to the number of nodes?

  - nothing guarantees that the `rng` containers will be distributed evenly

  - if we add nodes later, they will not automatically run a copy of `rng`

  - if we remove (or reboot) a node, one `rng` container will restart elsewhere

- Instead of a `deployment`, we could use a `daemonset`

---

## Daemon sets in practice

- Daemon sets are great for cluster-wide, per-node processes:

  - `kube-proxy`

  - `weave` (our overlay network)

  - monitoring agents

  - hardware management tools (e.g. SCSI/FC HBA agents)

  - etc.

- They can also be restricted to run [only on some nodes](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/#running-pods-on-only-some-nodes)

---

## Daemon sets - you won't really use them

- Daemon sets are not something you would normally have to worry about.

- So why mention them at all?

- Well, we do use them for **infrastructure** related operations, such as

  - `kube2iam` for identity access management

  - `scalyr-agent` to record all logs into [scalyr](https://www.scalyr.com/) for analysis

  - `sysdig-agent` for security, monitoring & forensics using [sysdig](https://sysdig.com/)

---

class: pic

![Node, pod, container](images/k8s_training_daemonset.png)

---

# Labels and debugging

- When a pod is misbehaving, we can delete it: another one will be recreated

- But we can also change its labels

- It will be removed from the load balancer (it won't receive traffic anymore)

- Another pod will be recreated immediately

- But the problematic pod is still here, and we can inspect and debug it

- We can even re-add it to the rotation if necessary

  (Very useful to troubleshoot intermittent and elusive bugs)

---

## Labels and advanced rollout control

- Conversely, we can add pods matching a service's selector

- These pods will then receive requests and serve traffic

- Examples:

  - one-shot pod with all debug flags enabled, to collect logs

  - pods created automatically, but added to rotation in a second step
    <br/>
    (by setting their label accordingly)

- This gives us building blocks for canary and blue/green deployments

---

## Removing a pod from the load balancer

- let's reuse our old friend `pingpong` and:

  - Run it with a specific label
  
  - Remove (not destroy!) the pod by altering the pod label

---

## Creating our labelled deployment

- let's start by creating a new deployment:

.exercise[
- in a separate window watch the pods so you can what happens:
  ```bash
  kubectl get pods -w
  ```
- Let's use a new deployment to ping `1.1.1.1`, Cloudflare's 
  [public DNS resolver](https://blog.cloudflare.com/announcing-1111/):
  ```bash
  kubectl run labeldemo --labels isactive=yes --image alpine ping 1.1.1.1
  ```
]

  notice the change to the run command: `--labels isactive=yes` - we're specifying a label to be applied to that deployment

---

## What happened?
- Unsurprisingly, we created a new pod that is constantly pinging.

- So how is this different from our first attempt at this?

.exercise[
- using the label option, get the pods that are active and not active:
```bash
  kubectl get pods -l isactive=yes
  kubectl get pods -l isactive=no
```
]
- What do you see?

---

## Labels have been applied

- you should have seen something like this:

```
$ kubectl get pods -l isactive=yes
NAME                         READY     STATUS    RESTARTS   AGE
labeldemo-854f7df968-p2rk9   1/1       Running   0          17s

$ kubectl get pods -l isactive=no
No resources found.
```

- our pod has an `isactive=yes` label applied to it

- and there are no `isactive=no` pods running

---

## Altering a label

- Now we wil edit one of our pods to make it "inactive" (using the handy `patch` command)
.exercise[
  ```bash
PATCH='
    metadata:
      labels:
        isactive: "no"
    '
    kubectl patch pod/labeldemo-XXXXXXXXXX-YYYYY -p "$PATCH" 
```
]

- Remember to watch the pods while you do this

---

## Checking our pods

- you should have seen a new `pod` being created.

- let's re-look at the state of our pods using the two label commands we used previously

- You should now see an "inactive" pod listed:

```
$ kubectl get pods -l isactive=yes
NAME                         READY     STATUS    RESTARTS   AGE
labeldemo-854f7df968-lr62s   1/1       Running   0          30s

$ kubectl get pods -l isactive=no
NAME                         READY     STATUS    RESTARTS   AGE
labeldemo-854f7df968-p2rk9   1/1       Running   0          14m
```

---

## What happened?

- We changed the `label` of our `pod`

- The `replicaset` then noticed that the `pod` no longer met the "conditions" of the deployment and created a new `pod`

- Most importantly, the old `pod` was **not** removed

- If the service on this `pod` was problematic then we could safely attach a debugger without impacting the customers in any way.
