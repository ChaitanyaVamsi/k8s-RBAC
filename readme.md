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

```
         ┌───────────────┐
         │   IAM User    │
         │  (e.g., suresh)│
         └───────┬───────┘
                 │ AWS Credentials
                 ▼
        ┌─────────────────┐
        │   AWS EKS API   │
        │   (Authentication) │
        └────────┬────────┘
                 │ Checks aws-auth ConfigMap
                 ▼
       ┌─────────────────────┐
       │  Kubernetes User /  │
       │      Group          │
       │(e.g., roboshop-trainee) │
       └─────────┬───────────┘
                 │ RBAC Permissions
                 ▼
        ┌─────────────────┐
        │ Kubernetes API  │
        │ Authorization   │
        │ (Role/RoleBinding) │
        └─────────┬────────┘
                 │
                 ▼
            Allowed Actions
      (get pods, list pods, etc.)
```

---

### Explanation:

1. **IAM User/Role**

   - Your AWS identity (`suresh`, `akash`, or Node IAM Role).

2. **AWS EKS API Authentication**

   - EKS verifies the identity using IAM credentials.
   - Maps IAM identity → Kubernetes identity via **`aws-auth` ConfigMap**.

3. **Kubernetes User/Group**

   - The mapped username and group (`roboshop-trainee` or `roboshop-admin`).
   - This is now a valid Kubernetes identity.

4. **Kubernetes RBAC Authorization**

   - Kubernetes checks **Role / RoleBinding / ClusterRoleBinding** to see what this user/group can do.
   - For example:

     - `roboshop-trainee` → read-only access to pods
     - `roboshop-admin` → full cluster access

5. **kubectl / API Actions**

   - Only allowed if both authentication and authorization pass.

---

✅ **Key takeaway**:

- `aws-auth` is **only for authentication** (mapping AWS IAM → Kubernetes).
- RBAC is **for authorization** (what the user can actually do).
- Both together control who can access the cluster and what they can do.

---

## How authentication vs authorization works in EKS

1. **Authentication (aws-auth)**:

   - Who are you?
   - Done by IAM → AWS validates your identity.

2. **Authorization (RBAC in Kubernetes)**:

   - What can you do?
   - Done by Kubernetes roles/groups → e.g., `roboshop-trainee` can `get/list/watch pods` only.

So `aws-auth` only **maps IAM identity → Kubernetes identity/group**. After that, Kubernetes RBAC decides what actions are allowed.

---

| Resource           | Recommended Name           |
| ------------------ | -------------------------- |
| Role               | roboshop-admin-role        |
| RoleBinding        | roboshop-admin-rolebinding |
| ClusterRole        | roboshop-storage-ro-cr     |
| ClusterRoleBinding | roboshop-storage-ro-crb    |
