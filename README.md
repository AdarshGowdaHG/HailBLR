# HailBLR: containerised cab booking

frontend (nginx) -> /api -> backend (Node/Express) -> MongoDB

## Run with Docker Compose
    docker compose up --build
    open http://localhost:8080

## Run on Kubernetes (minikube)
    minikube start
    minikube addons enable ingress metrics-server
    eval $(minikube docker-env)              # build images inside minikube
    docker build -t hailblr-backend:1.0 ./backend
    docker build -t hailblr-frontend:1.0 ./frontend
    kubectl apply -f k8s/
    kubectl -n hailblr get pods
    kubectl -n hailblr port-forward svc/frontend 8080:80    # then open http://localhost:8080
    # or use the ingress: add "$(minikube ip) hailblr.local" to /etc/hosts

## Handy demo commands
    kubectl -n hailblr scale deployment/backend --replicas=4     # manual scaling
    kubectl -n hailblr delete pod -l app=backend                 # self-healing: pods come back
    kubectl -n hailblr rollout restart deployment/backend        # zero-downtime rolling update
    kubectl -n hailblr get hpa                                   # autoscaling status

## CI/CD
`.github/workflows/ci.yml` builds both images, pushes them to GitHub Container Registry, then deploys to the cluster.
Add a `KUBE_CONFIG` repository secret for the deploy job.
