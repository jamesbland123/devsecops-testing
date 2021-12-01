# Misc DevSecOps examples/demos
All examples are for mac but can be adapted for windows desktops. 

- This repo has submodules. Clone with ```git clone --recurse-submodules```. If you forget you'll need to run ```git submodule init``` and ```git submodule update```

## Hadolint - Docker security
```
brew install hadolint
```

Install the vscode extension for hadolint and open hadolint-example Dockerfile. You might have to restart vscode, but all findings will be listed under problems.

## CFN Nag - Cloud Formation security best practice scanning
```
brew install ruby brew-gem
brew gem install cfn-nag
```

Install vscode extensions for cfn-nag. Open cfn-nag/cfn_nag_examples/cfn/stack.yml and/or volume.yaml. Issues should be listed under problems detected.

## Snyk
Install the Snyk vscode extension and follow the instructions. Snyk should automatically scan all the files and detect issues in snyk-example/nodejs-goof.

## Argo CD Installation - Deployments. 
ArgoCD is a gitOps deployment system that can be used to automate the deployment process so running software can be tested.
1. Install Argo CD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
2. Install Argo CD CLI
```
brew install argocd
```

3. Optional (redirect can be used if you don't want to expose a public interface): Expose the service through load balancer
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

## Gatekeeper example
1. Install Gatekeeper
```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/release-3.7/deploy/gatekeeper.yaml

# Validate pods - all services should show as running
kubectl get pods -n gatekeeper-system

# View Gatekeeper logs
kubectl logs -l control-plane=audit-controller -n gatekeeper-system
kubectl logs -l control-plane=controller-manager -n gatekeeper-system
```

2. Install constraint template and constraint
```
cd gatekeeper/priv-demo

kubectl create -f ./constrainttemplate.yaml
kubectl create -f ./constraint.yaml
```

3. Validate constraint
```
kubectl get constraint
kubectl get constrainttemplate
```

4. Test constraint
```
kubectl create -f .test-constraint.yaml
```

5. Optional: Additional example constraints in gatekeeper/gatekeeper-library 