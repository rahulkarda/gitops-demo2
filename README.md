# 🚀 GitOps Deployment with ArgoCD

This project demonstrates **GitOps** using **ArgoCD** to automate deployments in a Kubernetes cluster.  
Changes made in **GitHub** are automatically synced to **Kubernetes**, ensuring a **declarative & automated deployment pipeline**.  

---

## **📌 Prerequisites**
Before starting, ensure you have:
- **Minikube or a Kubernetes cluster** running
- **ArgoCD installed & configured**
- **Helm installed** (`brew install helm` or `sudo apt install helm`)
- **kubectl installed** (`brew install kubectl` or `sudo apt install kubectl`)
- **Git & GitHub repository access**

---

## **🔹 Step 1: Deploy ArgoCD**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### **➡️ Access ArgoCD UI**
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
- Open: **[http://localhost:8080](http://localhost:8080)**
- Login:
  ```bash
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
  ```
  **Username:** `admin`
  **Password:** (Use the above command output)

---

## **🔹 Step 2: Add GitHub Repository to ArgoCD**
```bash
argocd repo add https://github.com/rahulkarda/gitops-demo2.git \
  --username rahulkarda \
  --password <your-github-token>
```
✅ **Replace `<your-github-token>` with your actual GitHub PAT**

---

## **🔹 Step 3: Deploy the GitOps Application**
```bash
argocd app create gitops-demo2 \
  --repo https://github.com/rahulkarda/gitops-demo2.git \
  --path gitops-demo/charts \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated
```
- **Check ArgoCD Application Status**
  ```bash
  argocd app get gitops-demo2
  ```
- **Manually Sync (if needed)**
  ```bash
  argocd app sync gitops-demo2
  ```

---

## **🔹 Step 4: Access the Deployed Application**
### **1️⃣ Using LoadBalancer (If Available)**
```bash
kubectl get svc -n default
```
Check the `EXTERNAL-IP` and open:
```
http://<EXTERNAL-IP>:80
```

### **2️⃣ Using Port Forwarding (If No LoadBalancer)**
```bash
kubectl port-forward svc/gitops-demo-service 9090:80 -n default
```
Now, access the app:
```
http://localhost:9090
```

---

## **🔹 Step 5: Demonstrate GitOps Workflow**
### **📝 Make a Change in GitHub**
1. Open **[https://github.com/rahulkarda/gitops-demo2](https://github.com/rahulkarda/gitops-demo2)**
2. Edit `public/index.html`
3. Change:
   ```html
   <h1>Welcome to GitOps Deployment!</h1>
   ```
   **To:**
   ```html
   <h1>GitOps Deployment with ArgoCD 🚀</h1>
   ```
4. **Commit & push the change**  
5. **Wait for ArgoCD to auto-sync**

---

## **🔹 Step 6: Verify the Update**
- Open **http://localhost:9090** and check if the change is reflected.
- If not, manually trigger sync:
  ```bash
  argocd app sync gitops-demo2
  ```
- Check if new pods are created:
  ```bash
  kubectl get pods -n default
  ```

✅ **Your change is now live without manually deploying anything!** 🎉  

---

## **🛠️ Troubleshooting**
| Issue | Fix |
|-------|-----|
| ArgoCD UI Not Opening | Run `kubectl port-forward svc/argocd-server -n argocd 8080:443` |
| Pods Stuck in "Pending" | Check `kubectl describe pod -n default <pod-name>` |
| Service External-IP is `<pending>` | Use `kubectl port-forward` instead |
| Changes Not Updating | Restart pod with `kubectl rollout restart deployment gitops-demo2 -n default` |

---

## **🎯 Conclusion**
This setup ensures **fully automated deployments** using GitOps with ArgoCD!  
Now, any code change in GitHub is **automatically deployed** in Kubernetes.  

🚀 **Try modifying the app again, commit the change, and watch ArgoCD update it live!**