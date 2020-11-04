---
layout: post
title:  "Ansible Tower Container Groups with OpenShift"
date:   2020-10-23 
---

What are they? Serverless functions?.. a new Tower feature?.. Well they are an integration to run your playbooks
 in OpenShift/Kubernetes pods. Check out this video to see this feature in action.



```yaml

apiVersion: v1
kind: Namespace
metadata: 
  name: ansible-tower

```

```yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: tower
  namespace: ansible-tower

```

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
