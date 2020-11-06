---
layout: post
title:  "Ansible Tower Container Groups with OpenShift"
date:   2020-10-23
image: lens.jpg
---

What are they? Serverless functions?.. a new Tower feature?.. Well they are an integration to run your playbooks
 in OpenShift/Kubernetes pods. Check out this video to see this feature in action.



<iframe width="560" height="315" src="https://www.youtube.com/embed/fBNTYOovtkI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



This is made possible by using Ansible Runner.

The project can be found under the Ansible org on GitHub.

https://github.com/ansible/ansible-runner/tree/stable/1.4.x

Below I will detail the steps required to set this up. Then show you how to build some Dockerfiles that use Ansible Runner



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




<script src="https://utteranc.es/client.js"
        repo="colin-mccarthy/thenetwork.engineer.blog_9.github.io"
        issue-term="pathname"
        theme="preferred-color-scheme"
        crossorigin="anonymous"
        async>
</script>

