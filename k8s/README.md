# Kubernetes commands
## Rollout commands:
    ```bash
    microk8s kubectl rollout restart deployment <deployment-name> -n <namespace>

    microk8s kubectl get deployments -n <namespace>
    ```
## Cronjob commands:
    ```bash
    microk8s kubectl create cronjob <cronjob-name> --image=<image> --schedule="<schedule>" -n <namespace>

    microk8s kubectl get cronjobs -n <namespace>
    microk8s kubectl delete cronjob <cronjob-name> -n <namespace>
    ```