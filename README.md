# Raspberry Pi Initialization with Terraform

This is for Provisioning a Rpi2/3/4 K3s cluster using terraform.

## Manual Steps

### Raspberry

Burn image to sd card.

Inject file `/boot/ssh` to autostart ssh. <https://www.raspberrypi.org/documentation/remote-access/ssh/README.md>

### terraform.tfvars

Create a file `terraform.tfvars` for easy adding variable defaults.
An Example File `terraform.example.tfvars` is included.

## Deployment

My cluster has the following components:

  Name         | Ram  | IP Address    | Hostname  | Role   |
---------------|------|---------------|-----------|--------|
Raspberry Pi 2 | 1 GB | 192.168.10.20 | Raspi2-01 | Master |
Raspberry Pi 3 | 1 GB | 192.168.10.21 | Raspi3-01 | Worker |
Raspberry Pi 4 | 8 GB | 192.168.10.22 | Raspi4-01 | Worker |

### Servernode

```cmd
  terraform workspace new rpi2
  terraform workspace select rpi2
  terraform init
  terraform plan -var-file="terraform.rpi2.tfvars"
  terraform apply -var-file="terraform.rpi2.tfvars"
```

### Workernode

```cmd
  terraform workspace new rpi3
  terraform workspace select rpi3
  terraform init
  terraform plan -var-file="terraform.rpi3.tfvars"
  terraform apply -var-file="terraform.rpi3.tfvars"
```

```cmd
  terraform workspace new rpi4
  terraform workspace select rpi4
  terraform init
  terraform plan -var-file="terraform.rpi4.tfvars"
  terraform apply -var-file="terraform.rpi4.tfvars"
```

## Kubernetes

### kubeconfig

1. Get kubectl <https://kubernetes.io/docs/tasks/tools/install-kubectl/>
2. Configuration of kubectl <https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/>
3. Test access
   - `--kubeconfig` flag

      ```cmd
      kubectl --kubeconfig kubeconfig get node
      ```

   - `KUBECONFIG` environment variable

      ```cmd
      set KUBECONFIG=kubeconfig
      kubectl get node
      ```

4. Create Kubernetes Dashboard

   ```cmd
   kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
   kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
   kubectl -n kubernetes-dashboard describe secret admin-user-token
   kubectl proxy
   ```

5. Login Kubernetes Dashboard  
   <http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/>
