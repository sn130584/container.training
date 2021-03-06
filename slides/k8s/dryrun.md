# Authoring YAML

- There are various ways to generate YAML with Kubernetes, e.g.:

  - `kubectl run`

  - `kubectl create deployment` (and a few other `kubectl create` variants)

  - `kubectl expose`

- When and why do we need to write our own YAML?

- How do we write YAML from scratch?

---

## The limits of generated YAML

- Many advanced (and even not-so-advanced) features require to write YAML:

  - pods with multiple containers

  - resource limits

  - healthchecks

  - DaemonSets, StatefulSets

  - and more!

- How do we access these features?

---

## We don't have to start from scratch

- Create a resource (e.g. Deployment)

- Dump its YAML with `kubectl get -o yaml ...`

- Edit the YAML

- Use `kubectl apply -f ...` with the YAML file to:

  - update the resource (if it's the same kind)

  - create a new resource (if it's a different kind)

- Or: Use The Docs, Luke

  (the documentation almost always has YAML examples)

---

## Generating YAML without creating resources

- We can use the `--dry-run` option

.exercise[

- Generate the YAML for a Deployment without creating it:
  ```bash
  kubectl create deployment web --image nginx -o yaml --dry-run
  ```

]

- We can clean up that YAML even more if we want

  (for instance, we can remove the `creationTimestamp` and empty dicts)

---

## Server-side dry run

- Since Kubernetes 1.13, we can use [server-side dry run and diffs](https://kubernetes.io/blog/2019/01/14/apiserver-dry-run-and-kubectl-diff/)

- This is a big deal when using `kubectl apply -f` on the same YAML file over and over

- It outputs a list of resources and if they were created or updated
---

## Advantages of server-side dry run over normal dry run

- The YAML is verified much more extensively

- The only step that is skipped is "write to etcd"

- YAML that passes server-side dry run *should* apply successfully

  (unless the cluster state changes by the time the YAML is actually applied)

---

## `kubectl diff`

- Kubernetes 1.13 also introduced `kubectl diff`

- `kubectl diff` does a server-side dry run, *and* shows differences

.exercise[

- Try `kubectl diff` on the YAML that we tweaked earlier:
  ```bash
  kubectl apply -f https://bret.run/app.yml
  kubectl diff -f https://bret.run/appdiff.yml
  ```

]

- Yep `kubectl` can use URL's for YAML!

---

## Advantage of YAML

- Using YAML (instead of `kubectl run`/`create`/etc.) allows to be *declarative*

- The YAML describes the desired state of our cluster and applications

- YAML can be stored, versioned, archived (e.g. in git repositories)

- To change resources, change the YAML files

  (instead of using `kubectl edit`/`scale`/`label`/etc.)

- Changes can be reviewed before being applied

  (with code reviews, pull requests ...)

- This workflow is sometimes called "GitOps"

  (there are tools like Weave Flux, GitKube, or Jenkins X to facilitate it)

---

## YAML in practice

- Get started with `kubectl run`/`create`/`expose`/etc.

- Dump the YAML with `kubectl get -o yaml`

- Tweak that YAML and `kubectl apply` it back

- Store that YAML for reference (for further deployments)

- Feel free to clean up the YAML:

  - remove fields you don't know

  - check that it still works!

- That YAML will be useful later when using e.g. Kustomize or Helm
