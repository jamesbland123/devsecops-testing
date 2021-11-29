# devsecops-demo
Misc DevSecOps demos 

Mac
```
brew install hadolint

brew install ruby brew-gem
brew gem install cfn-nag

```

Install vscode extensions for hadolint, cfn-nag, Snyk


# Argo CD Installation
1. Install Argo CD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
2. Install Argo CD CLI
```
brew install argocd
```

3. Expose the service through load balancer
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}' 
```

4. Login to the service and change the password
```
# Grab the IP from the first command
kubectl get services --namespace argocd

# Get the password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo 

# ARGOCD is the endpoint for argocd-server
argocd login <ARGOCD_SERVER>

# Change the admin password
argocd account update-password
```

5. Deploy a sample app via CLI
```
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
```

6. Sync the app via CLI
```
argocd app get guestbook

argocd app sync guestbook
```

7. Optional make the guestbook public
```
kubectl patch svc guestbook-ui -n default -p '{"spec": {"type": "LoadBalancer"}}'
```





