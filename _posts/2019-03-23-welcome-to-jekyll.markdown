---
layout: post
title:  "Ansible Tower Container Groups with OpenShift"
date:   2020-10-23
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


Ansible Tower

1. Create the Credential

pic coming soon

CREDENTIAL TYPE

Choose the OpenShift or Kubernetes API Bearer Token type.

Copy the token from the service account created in that project (User Management > Service Accounts > (tower) ←The one you made in the earlier steps)


pic coming soon

OPENSHIFT OR KUBERNETES API ENDPOINT

I grabbed the short version from the oc login command, it should look like (https://api.foo.openshift.io:6443)

2. Create the Container Group

In Ansible Tower under Instance Groups > The green plus symbol > CREATE CONTAINER GROUP


pic coming soon


3. Configure the Container Group

Give it a name and select the OpenShift or Kubernetes API Bearer Token we created earlier in the steps above.


pic coming soon


Now the fun part, paste in your Kubernetes Pod manifest. I like to use this one as a default when first getting started.

Pod manifest



pic coming soon


What about module dependencies?

As we know in Ansible Tower we need to install our dependencies in custom virtual environments. How can we accomplish this when using Container Groups? Well, you can make your own container images with the dependencies already installed. Start by creating a base Ansible Runner image in Quay. This way you control the software delivery supply chain. Mine is listed below.

https://quay.io/repository/colin_mccarthy/ansible-runner

I built this by cloning the Ansible Runner Github repo and then building from Dockerfile.dev

Runner with pysnow package installed

I then made a new image, I went ahead and installed the dependency (pysnow) via pip right in the Dockerfile.

Dockerfile




code coming soon




I built a new image in Quay based on this Dockerfile and now its ready to use in my Pod manifest like this.














<script src="https://utteranc.es/client.js"
        repo="colin-mccarthy/thenetwork.engineer.blog_9.github.io"
        issue-term="pathname"
        theme="preferred-color-scheme"
        crossorigin="anonymous"
        async>
</script>

