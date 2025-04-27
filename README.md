# coach19-26apr ingress controller


- Step 1: Connect to a shared EKS cluster	1  
 ![](step1-connect-to-EKS-cluster.png)  
 
- Step 2: Create a namespace	1  
 ![](step2-create-namespace.png)  

- Step 3: Sample Deployment & Service	2  
  ![](step3-apply-deployment-and-service-yaml.png)  

  ![](step3-validate-describe-deployment.png)  

  ![](step3-verify-service-and-pod-running.png)  

  ![](step3-split-screen-test-forwarding-service.png)  

- Step 4 (Optional): Testing your service using Netshoot

  
```
apiVersion: v1
kind: Pod
metadata:
  name: netshoot
  namespace: chrisy-eks-activity
spec:
  containers:
  - name: netshoot
    image: nicolaka/netshoot:latest
    command: ["/bin/bash"]
    args: ["-c", "while true; do ping localhost; sleep 60;done"]
```
  
- Step 4: Creating a Ingress Resource with ExternalDNS & Nginx Controller  
  ![](step4-validate-netshoot.png)
- Step 5: Verify record creation in Route53  
  ![](step5-apply-get-validate-ingress-resource.png)  

  ![](step5-describe-ingress.png)  

- Step 6: Enable LetsEncrypt TLS cert on your Ingress  

  ![](step6-curl-http-ok-test-service-in-route53.png)

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: chrisy-echo-app-ingress
  namespace: chrisy-eks-activity
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod" # <----- appended
    external-dns.alpha.kubernetes.io/hostname: "chrisy-echoapp.sctp-sandbox.com" # Route53 Record
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - "chrisy-echoapp.sctp-sandbox.com" # Route53 Record
      secretName: chrisy-tls  # <----- appended
  rules:
  - host: "chrisy-echoapp.sctp-sandbox.com" # Route53 Record
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: chrisy-echo-service
            port:
              number: 80
```
  
  
- Step 7: Curl your HTTPS endpoint  

  ![](step7-createTLScert-check.png)

  ![](step7-view-TLS-Key-part1.png)

  ![](step7-view-TLS-Key-part2.png)

  ![](step7-view-TLS-Key-part3.png)

  ![](step7-view-TLScert-and-secret.png)
