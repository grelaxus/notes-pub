# Run proxy to acces Kube API via curl
```sh
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

in another terminal:
```sh
$ curl -H "Content-Type: application/json" -X PUT --data-binary @developer-ns.json http://127.0.0.1:8001/api/v1/namespaces/developer/finalize
```
see how to [remove Namespace stuck in terminating state](https://github.com/grelaxus/notes-pub/edit/master/k8s/k8s-notes.md#remove-namespace-stuck-in-terminating-state-after-delete-)
```sh
curl -k -H "Content-Type: application/json" -X GET http://127.0.0.1:8001/
curl -k -H "Content-Type: application/json" -X GET http://127.0.0.1:8001/.well-known/openid-configuration
```


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

# Troubleshooting
## [Remove Namespace stuck in terminating state after delete ](https://linuxhelp4u.blogspot.com/2019/01/kubernetes-remove-namespace-stuck-in.html).  
```sh
kubectl get namespaces | grep developer
developer                                    Terminating   30d
```
Describe namespace shows following error:
```sh
Conditions:
  Type                                         Status  LastTransitionTime               Reason                  Message
  ----                                         ------  ------------------               ------                  -------
  NamespaceDeletionDiscoveryFailure            True    Wed, 25 May 2022 18:30:16 -0700  DiscoveryFailed         Discovery failed for some groups, 1 failing: unable to retrieve the complete list of server APIs: metrics.k8s.io/v1beta1: the server is currently unable to handle the request
```
More details in json
```sh
oc get ns developer -o json > developer-ns.json
```
```json

//...
      {
        "type": "NamespaceDeletionContentFailure",
        "status": "False",
        "lastTransitionTime": "2022-05-26T01:17:25Z",
        "reason": "ContentDeleted",
        "message": "All content successfully deleted, may be waiting on finalization"
      },
//...
```
The finalizer "kubernetes" needs to be remomve. vi into the developer-ns.json and remove the finalizer:
```json
    },
    "spec": {
        "finalizers": [
        ]
    },
```
Run kubectl proxy in some other terminal
```sh
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```
back to the original terminal (or any other) and run following curl:
```sh
$ curl -H "Content-Type: application/json" -X PUT --data-binary @developer-ns.json http://127.0.0.1:8001/api/v1/namespaces/developer/finalize
```
NOTE: there is '@'prefix before the json file name. Also don't forget to replace the namespace in the URL
