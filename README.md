# Steps to reproduce
It is assumed that ArGoCD v2.13.2+dc43124 is installed 

 1. Prepare kubernetes cluster. Add externalDNS CRD and create namespace
 `kubectl apply -f https://kubernetes-sigs.github.io/external-dns/latest/docs/contributing/crd-source/crd-manifest.yaml
kubectl create ns external-dns`
 2. Create App in ArgoCD, with following settings (also provided in file: argocd-app.yaml)

>     project: default
>     destination:
>       server: https://kubernetes.default.svc
>       namespace: external-dns
>     ignoreDifferences:
>       - group: apiextensions.k8s.io
>         kind: CustomResourceDefinition
>     sources:
>       - repoURL: https://github.com/dirkvdplas/argocd-externaldns-ignoredifferences.git
>         path: manifests
>         targetRevision: master
>       - repoURL: registry-1.docker.io/bitnamicharts
>         path: external-dns
>         targetRevision: 8.7.1
>         chart: external-dns

 3. Introduce a change in the external-dns custom resource. Change
    recordTTL to another value
 `kubectl -n external-dns edit DNSEndpoint ns-record`
 4. After the change of DNSendpoint  ArgoCD will  report outofSync, appearantly the "ignoreDifferences" did not have any effect
