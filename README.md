## Getting Started

### Dependencies
#### Local Environment
1. Python Environment - run Python 3.6+ applications and install Python dependencies via `pip`
2. Docker CLI - build and run Docker images locally
3. `kubectl` - run commands against a Kubernetes cluster
4. `helm` - apply Helm Charts to a Kubernetes cluster

#### Remote Resources
1. AWS CodeBuild - build Docker images remotely
2. AWS ECR - host Docker images
3. Kubernetes Environment with AWS EKS - run applications in k8s
4. AWS CloudWatch - monitor activity and logs in EKS
5. GitHub - pull and clone code

### Setup
#### 1. Configure a Database
Set up a Postgres database using a Helm Chart.

1. Set up Bitnami Repo
```bash
helm repo add postgres https://charts.bitnami.com/bitnami
```
2. Add configmap to your EKS (it includes secrets and env variable)
```bash
kubectl apply -f .\deployment\configmap.yaml
```
3. Install PostgreSQL using db-deployment (it uses latest release from bitnami)
```bash
kubectl apply -f .\db\db-deployment.yaml
```
4. Feed database using .sql files in \db folder (you need to have postgres pod name and forward here username and database name)
```bash
Get-Content .\db\<file to feed>.sql | kubectl exec -i postgres-<pod-number> -- psql -U <username> -d <database_name> 
for example
Get-Content .\db\2_seed_users.sql | kubectl exec -i postgres-5bb9645444-dflgv -- psql -U postgres -d postgres  
```

#### 2. Setup ECS and CloudBuild
1. If you want to automate deployment CI/CD you need to push this repo to your github account and connect it to ECS with provided buildspec.yml (CloudBuild)
   This will automatically build and push to ECS new release of your application.

#### 3. Setup Application
1. If ECR is set up and CloudBuild is working you can push coworking.yaml file to EKS
```bash
kubectl apply -f .\deployment\coworking.yaml
``` 
2. If your readiness and health check is ok (you can inspect pod) you are ready to go
```bash
kubectl logs deployment/coworking
```