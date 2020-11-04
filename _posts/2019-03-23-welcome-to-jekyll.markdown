---
layout: post
title:  "Ansible Tower Container Groups with OpenShift"
date:   2020-10-23 
---

What are they? Serverless functions?.. a new Tower feature?.. Well they are an integration to run your playbooks
 in OpenShift/Kubernetes pods. Check out this video to see this feature in action.



<iframe width="560" height="315" src="https://www.youtube.com/embed/fBNTYOovtkI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



1. Create a Project/Namespace in OpenShift named (ansible-tower). This can be done in the OpenShift console or via oc/kubectl.

```yaml

apiVersion: v1
kind: Namespace
metadata: 
  name: ansible-tower

```

2. Create a ServiceAccount named (tower).This can be done in the OpenShift console or via oc/kubectl.

```yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: tower
  namespace: ansible-tower

```

3. Create a Role named (pod-manager).This can be done in the OpenShift console or via oc/kubectl.

```yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-manager
  namespace: ansible-tower
rules:
  - verbs:
      - get
      - list
      - watch
      - create
      - update
      - patch
      - delete
    apiGroups:
      - ''
    resources:
      - pods
  - verbs:
      - create
    apiGroups:
      - ''
    resources:
      - pods/exec

```

4. Create a RoleBinding that binds the pod manager role to the tower service account.This can be done in the OpenShift console or via oc/kubectl.

```yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: ansible-tower
  namespace: ansible-tower
subjects:
  - kind: ServiceAccount
    name: tower
    namespace: ansible-tower
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-manager

```

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
