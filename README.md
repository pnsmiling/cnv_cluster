# cnv_cluster set up

### Copy ssh-key to nodes
```
ssh-copy-id root@master01 &&
ssh-copy-id root@master02 &&
ssh-copy-id root@master03 &&
ssh-copy-id root@worker01 &&
ssh-copy-id root@worker02 &&
ssh-copy-id root@worker03
```

### Add host on main VM
```
cat <<EOF >> /etc/hosts
10.0.4.18 master01
10.0.4.19 master02
10.0.4.20 master03
10.0.4.21 worker01
10.0.4.22 worker02
10.0.4.23 worker03
10.0.4.24 worker04
EOF
```

### Add host on remote VM
```
#!/bin/bash
declare -a hosts=("worker01" "worker02" "worker03")
for i in "${hosts[@]}"
do
   ssh root@$i 'cat <<EOF >> /etc/hosts
10.0.4.18 master01
10.0.4.19 master02
10.0.4.20 master03
10.0.4.21 worker01
10.0.4.22 worker02
10.0.4.23 worker03
10.0.4.24 worker04
EOF'
done
```

### Create Docker environment
```
docker run --rm -it --mount type=bind,source=/opt/cnv_cluster,dst=/kubespray \
                    --mount type=bind,source=/root/.ssh/id_ed25519,dst=/root/.ssh/id_rsa \
                    --mount type=bind,source=/root/.ssh/id_ed25519,dst=/home/it/.ssh/id_rsa \
                    quay.io/kubespray/kubespray:v2.27.0 bash
```

### Create cluster
```
ansible-playbook -i inventory/host.yaml cluster.yml --user=it \
                    --ask-pass --become --ask-become-pass
```

### Delete node
**Use --extra-vars "node=<nodename>,<nodename2>" to select the node(s)**
```
ansible-playbook -i inventory/host.yaml remove-node.yml --user=it \
                    --ask-pass --become --ask-become-pass \
                    --extra-vars "node=worker04"
```
