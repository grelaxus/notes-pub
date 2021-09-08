# Accessing k8s node without SSH

```sh
$ oc debug node/ip-10-0-132-143.eu-west-3.compute.internal
Starting pod/ip-10-0-132-143eu-west-3computeinternal-debug ...
To use host binaries, run `chroot /host`
Pod IP: 10.0.132.143
If you don't see a command prompt, try pressing enter.
sh-4.2# cat /etc/redhat-release 
Red Hat Enterprise Linux Server release 7.7 (Maipo)
sh-4.2#

    Once in the debug session, one can use chroot to change the apparent root directory to the one of the underlying host:

Raw

sh-4.2# chroot /host bash
[root@ip-10-0-132-143 /]#  
```

# Inspecting image pull secret
https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#inspecting-the-secret-regcred
```sh
kubectl get secret regcred --output=yaml
```
To get a decoded json:
```sh
kubectl get secret zts-dockerhub-token --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode
```
The output:
```sh
{
    "auths": {
        "https://index.docker.io/v1/": {
            "auth": "em.........DUw"
        }
    }
}
```

