## on cluster

kubectl get configmap aws-auth -n kube-system -o yaml -> never delete this
kubectl get configmap aws-auth -n kube-system -o yaml > aws-auth-backup.yaml -> take backup

kubectl get roles -n roboshop - get roles
kubectl delete roles roboshop-trainee -n roboshop

kubectl get rolebindings -n roboshop

## kubectl on different machine

step 1 : aws configure
step 2 : aws eks update-kubeconfig --region <region> --name <cluster_name>

check cluster name in eks
aws eks update-kubeconfig --region us-east-1 --name sampleapp

| Kubernetes  | Office Example               |
| ----------- | ---------------------------- |
| Role        | Job description              |
| RoleBinding | HR assigning job to employee |
| User        | Employee                     |
| Namespace   | Department                   |
