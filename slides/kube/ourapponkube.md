class: title

Our app on Kube

---

## What's on the menu?

In this part, we will:

<!-- - **build** images for our app, -->

<!-- - **ship** these images with a registry, -->

- **run** deployments using pre-baked images,

- expose these deployments so they can communicate with each other,

- expose the web UI so we can access it from outside.

---

## The plan

- Create deployments using the pre-baked images

- Expose (with a ClusterIP) the services that need to communicate

- Expose (with a NodePort) the WebUI

---

class: extra-details

## Avoiding the `latest` tag

.warning[Make sure that you've set the `TAG` variable properly!]

- If you don't, the tag will default to `latest`

- The problem with `latest`: nobody knows what it points to!

  - the latest commit in the repo?

  - the latest commit in some branch? (Which one?)

  - the latest tag?

  - some random version pushed by a random team member?

- If you keep pushing the `latest` tag, how do you roll back?

- Image tags should be meaningful, i.e. correspond to code branches, tags, or hashes

---

## Deploying all the things

- We can now deploy our code (as well as a redis instance)

.exercise[

- Deploy `redis`:
  ```bash
  kubectl run redis --image=redis
  ```

- Deploy everything else:
  ```bash
    for SERVICE in hasher rng webui worker; do
      kubectl run $SERVICE --image=$REGISTRY/$SERVICE:$TAG
    done
  ```

]

---

## Is this working?

- After waiting for the deployment to complete, let's look at the logs!

  (Hint: use `kubectl get deploy -w` to watch deployment events)

.exercise[

- Look at some logs:
  ```bash
  kubectl logs deploy/rng
  kubectl logs deploy/worker
  ```

]

🤔 `rng` is fine ... But not `worker`.

💡 Oh right! We forgot to `expose`.

---

# Exposing services internally

- Three deployments need to be reachable by others: `hasher`, `redis`, `rng`

- `worker` doesn't need to be exposed

- `webui` will be dealt with later

.exercise[

- Expose each deployment, specifying the right port:
  ```bash
  kubectl expose deployment redis --port 6379
  kubectl expose deployment rng --port 80
  kubectl expose deployment hasher --port 80
  ```

]

---

## Is this working yet?

- The `worker` has an infinite loop, that retries 10 seconds after an error

.exercise[

- Stream the worker's logs:
  ```bash
  kubectl logs deploy/worker --follow
  ```

  (Give it about 10 seconds to recover)

<!--
```keys
^C
```
-->

]

We should now see the `worker`, well, working happily.

---

# Exposing services for access

- Now we would like to access the Web UI

- We will expose it with a `ClusterIP`

  (just like we did for the registry)

.exercise[

- Create a `ClusterIP` service for the Web UI:
  ```bash
  kubectl expose deploy/webui --type=ClusterIP --port=80
  ```

- Check the port that was allocated:
  ```bash
  kubectl get svc
  ```

]

---

## Accessing the web UI

- We can now connect to the service to view the web UI

.exercise[

- Use port forwarding: `kubectl port-forward svc/webui 8080:80`

- Open the web UI in your browser (http://localhost:8080/)

<!-- ```open http://localhost:80/``` -->

]

