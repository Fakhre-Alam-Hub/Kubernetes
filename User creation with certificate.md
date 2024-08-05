### Creation of a Private Key and a Certificate Signing Request (CSR)

1. Create private key

`openssl genrsa -out dave.key 4096`

Dave needs to make sure he:

Uses his name in the Common Name (CN) field: this will be used to identify him against the API Server.

Uses the group name in the Organisation (O) field: this will be used to identify the group against the API Server.

2. Create a Certificate Signing Request (CSR) for dave:

`openssl req -new -key dave.pem -out dave.csr -subj "/CN=dave"`

3. Obtain the base64-encoded representation of the CSR:

`cat dave.csr | base64 | tr -d "\n"`

4. Use the output to create a CertificateSigningRequest resource:

```
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dave
spec:
  request: <base64_encoded_csr>
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - digital signature
  - key encipherment
  - client auth
EOF
```

5. Approve the CertificateSigningRequest for dave:
   
`kubectl certificate approve dave`
`certificatesigningrequest.certificates.k8s.io/dave approved`

7. Check the CSR status:

`kubectl get csr`

7.Extract the signed certificate from the CertificateSigningRequest:

`kubectl get csr/dave -o jsonpath="{.status.certificate}" | base64 -d > dave.crt`

8. Create new user config file:

`kubectl --kubeconfig ~/.kube/config-dave config set-cluster preprod --insecure-skip-tls-verify=true --server=https://KUBERNETES-API-ADDRESS`

9.Set user credentials:

`kubectl --kubeconfig ~/.kube/config-dave config set-credentials dave --client-certificate=dave-user.crt --client-key=dave.pem --embed-certs=true`

Replace dave-user.crt and dave.pem with the paths to your user certificate and private key files respectively.

1O. Set context information:

`kubectl --kubeconfig ~/.kube/config-dave config set-context default --cluster=preprod --user=dave`

11.Use the context:

`kubectl --kubeconfig ~/.kube/config-dave config use-context default`

12.Now, your config-dave file is configured with the necessary cluster, user, and context information. You can use it with kubectl commands by passing --kubeconfig ~/.kube/config-dave. Make sure to replace placeholder values with your actual configuration details.

Example:

`kubectl --kubeconfig ~/.kube/config-dave get pods`

### RBAC Rules and attach to User







