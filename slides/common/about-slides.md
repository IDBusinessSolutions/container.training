## About these slides

- All the content is available in a public GitHub repository:

  https://@@GITREPO@@

- You can get updated "builds" of the slides there:

  http://container.training/

<!--
.exercise[
```open https://@@GITREPO@@```
```open http://container.training/```
]
-->

- Typos? Mistakes? Questions? Feel free to hover over the bottom of the slide ...

.footnote[.emoji[👇] Try it! The source file will be shown and you can view it on GitHub and fork and edit it.]

<!--
.exercise[
```open https://@@GITREPO@@/tree/master/slides/common/about-slides.md```
]
-->

---

class: extra-details

## Extra details

- This slide has a little magnifying glass in the top left corner

- This magnifying glass indicates slides that provide extra details

- Feel free to skip them if:

  - you are in a hurry

  - you are new to this and want to avoid cognitive overload

  - you want only the most essential information

- You can review these slides another time if you want, they'll be waiting for you ☺

---

class: pre-reqs

## Pre-Requisites 1

- Basic Docker knowledge

- If on Windows, it's probably a good idea to install `git-bash`!

- You will need the `kubeconfig` file

  - Copy to `~/.kube/config`

  - If you have a config file already, you can put this somewhere else and reference via an `ENV` variable.

  - E.g., `export KUBECONFIG=/path/to/config/file`

- You will need `kubectl`

  - If on MacOSX you can install via `brew`

---

class: pre-reqs

## Pre-Requisites 2

- You will each be provided a dedicated namespace within the Kubernetes cluster, based on your username.

- To set your default namespace, type the following command with your username:
```bash
kubectl config set-context $(kubectl config current-context) --namespace=<USERNAME_HERE>
```

- You can verify this worked by running:
```bash
kubectl config view | grep namespace:
```

---

class: pre-reqs

## Pre-Requisites 3

- Set-up the following environment variables

  - `export REGISTRY=quay.io/idbs`

  - `export TAG=1.0.0`
