
#### sealed-secrets 	-   Storing Encrypted Secrets in Version Control

Sealed-secrets is a Kubernetes controller that decrypts one-way encrypted secrets and creates in-cluster Secret objects 

You want to store all your Kubernetes manifests in version control and safely share
them (even publicly), including secrets.




1. Download kubeseal

$ GOOS=$(go env GOOS)

$ GOARCH=$(go env GOARCH)

$ wget https://github.com/bitnami/sealed-secrets/releases/download/v0.5.1/
       kubeseal-$GOOS-$GOARCH

$ sudo install -m 755 kubeseal-$GOOS-$GOARCH /usr/local/bin/kubeseal




2. Create SealedSecret CRD

$ kubectl create -f https://github.com/bitnami/sealed-secrets/releases/
                    download/v0.5.1/sealedsecret-crd.yaml

$ kubectl create -f https://github.com/bitnami/sealed-secrets/releases/
                    download/v0.5.1/controller.yaml

The result will be that you have a new custom resource and a new pod running in the kube-system

$ kubectl get customresourcedefinitions
NAME                        AGE
sealedsecrets.bitnami.com   34s

$ kubectl get pods -n kube-system | grep sealed
sealed-secrets-controller-867944df58-l74nk   1/1       Running   0          38s





3. You are ready to use SEALED SECRETS

$ kubectl create secret generic oreilly --from-literal=password=root -o json
                                        --dry-run > secret.json

$ cat secret.json
{
    "kind": "Secret",
    "apiVersion": "v1",
    "metadata": {
        "name": "oreilly",
        "creationTimestamp": null
    },
    "data": {
        "password": "cm9vdA=="
    }
}


` TIP
To create a manifest but not create the object on the API server, use the --dry-run option.
 This will print your manifest to stdout. If you want YAML, use the -o yaml
 option; and if you want JSON, use -o json.

$ kubeseal < secret.json > sealedsecret.json

IMPORTANT
You can now store sealedsecret.json safely in version control.
Only the private key stored in the sealed-secret controller
can decrypt it. 
Once you create the SealedSecret object, the controller will detect it, 
decrypt it, and generate the corresponding secret:





4. Decrypt it

$ kubectl create -f sealedsecret.json
sealedsecret "oreilly" created

$ kubectl get sealedsecret
NAME      AGE
oreilly   5s

$ kubectl get secrets
NAME       TYPE    DATA      AGE
...
oreilly    Opaque  1         5s
