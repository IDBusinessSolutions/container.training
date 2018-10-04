# Next Steps

- Some follow-up topics...

---

## Managing the configuration of our applications

- Two constructs are particularly useful: `secrets` and `config maps`

- They allow to expose arbitrary information to our containers

- **Avoid** storing configuration in container images

  (There are some exceptions to that rule, but it's generally a Bad Idea)

- **Never** store sensitive information in container images

  (It's the container equivalent of the password on a post-it note on your screen)

---

## Managing stack deployments

- The best deployment tool will vary, depending on:

  - the size and complexity of your stack(s)
  - how often you change it (i.e. add/remove components)
  - the size and skills of your team

- A few examples:

  - shell scripts invoking `kubectl`
  - YAML resources descriptions committed to a repo
  - [Helm](https://github.com/kubernetes/helm) (~package manager)
  - [Spinnaker](https://www.spinnaker.io/) (Netflix' CD platform)
  