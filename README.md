# Deploy Mastodon Helm Chart

## Add Helm Repo

- **URL:** https://artifacthub.io/packages/helm/codechem/mastodon
```
helm repo add codechem https://charts.codechem.com
```
## Values.yaml 

- Generate these values from a mastodon container 
`https://www.bentasker.co.uk/posts/blog/general/running-mastodon-in-docker-compose.html`

    - `secretKeyBase`
    - `otpSecret`
    - `vapidPrivateKey`
    - `vapidPublicKey`

- web ingress resource 
    ```
    ingress:
        enabled: true
        ## E.g.
        ## annotations:
        ##   kubernetes.io/ingress.class: nginx
        ##   kubernetes.io/tls-acme: "true"
        ##
        annotations:
        kubernetes.io/ingress.class: webapprouting.kubernetes.azure.com
        kubernetes.io/tls-acme: "true"
        ## E.g.
        ## hosts:
        ##   - host: mastodon-example.local
        hosts: 
        - host: api.zai.com.co
        ## E.g.
        - secretName: mastodon-web-tls-secret
            hosts:
            - api.zai.com.co
        tls: []
    ```
- `config.localDomain`: api.zai.com.co 
## TLS secret
```
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=api.zai.com.co/O=api.zai.com.co"
```
```
kubectl create secret tls mastodon-web-tls-secret --key tls.key --cert tls.crt
```
- this secret is added to the ingress configuration 

## Deploy
```
kubectl create ns mastodon
```
```
helm install -n mastodon mastodon codechem/mastodon -f /path/to/values.yaml --wait
```

