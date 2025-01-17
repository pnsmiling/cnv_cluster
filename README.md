# cnv_cluster

### Create Docker environment
```
docker run --rm -it --mount type=bind,source=/opt/kubespray/inventory/prod,dst=/kubespray/inventory \
                    --mount type=bind,source=/root/.ssh/id_ed25519,dst=/root/.ssh/id_rsa \
                    --mount type=bind,source=/root/.ssh/id_ed25519,dst=/home/it/.ssh/id_rsa \
                    quay.io/kubespray/kubespray:v2.27.0 bash
```

### Excute command
```
ansible-playbook -i inventory/host.yaml cluster.yml --user=it --ask-pass --become --ask-become-pass
```