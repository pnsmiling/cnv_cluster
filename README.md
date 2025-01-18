# cnv_cluster set up

### Copy ssh-key to nodes
```
ssh-copy-id it@prod_k8s_master01 &&
ssh-copy-id it@prod_k8s_master02 &&
ssh-copy-id it@prod_k8s_master03 &&
ssh-copy-id it@prod_k8s_ingress01 &&
ssh-copy-id it@prod_k8s_ingress02 &&
ssh-copy-id it@prod_k8s_ingress03 &&
ssh-copy-id it@prod_k8s_worker01 &&
ssh-copy-id it@prod_k8s_worker02 &&
ssh-copy-id it@prod_k8s_worker03 &&
ssh-copy-id it@prod_k8s_worker04 &&
ssh-copy-id it@prod_k8s_worker05 &&
ssh-copy-id it@prod_k8s_worker06 &&
ssh-copy-id it@prod_k8s_worker07 &&
ssh-copy-id it@prod_k8s_worker08 &&
ssh-copy-id it@prod_k8s_worker09 &&
ssh-copy-id it@prod_k8s_worker10 &&
ssh-copy-id it@prod_k8s_worker11 &&
ssh-copy-id it@prod_k8s_worker12
```

### Add host on main VM
```
cat <<EOF >> /etc/hosts
10.10.20.104 prod_k8s_master01
10.10.20.105 prod_k8s_master02
10.10.20.106 prod_k8s_master03

10.10.20.107 prod_k8s_ingress01
10.10.20.108 prod_k8s_ingress02
10.10.20.109 prod_k8s_ingress03

10.10.20.111 prod_k8s_worker01
10.10.20.112 prod_k8s_worker02
10.10.20.113 prod_k8s_worker03
10.10.20.114 prod_k8s_worker04
10.10.20.115 prod_k8s_worker05
10.10.20.116 prod_k8s_worker06
10.10.20.117 prod_k8s_worker07
10.10.20.118 prod_k8s_worker08
10.10.20.119 prod_k8s_worker09
10.10.20.120 prod_k8s_worker10
10.10.20.121 prod_k8s_worker11
10.10.20.122 prod_k8s_worker12
EOF
```

### Add host on remote VM
```
#!/bin/bash
declare -a hosts=("worker01" "worker02" "worker03")
for i in "${hosts[@]}"
do
   ssh it@$i 'cat <<EOF >> /etc/hosts
10.10.20.104 prod_k8s_master01
10.10.20.105 prod_k8s_master02
10.10.20.106 prod_k8s_master03

10.10.20.107 prod_k8s_ingress01
10.10.20.108 prod_k8s_ingress02
10.10.20.109 prod_k8s_ingress03

10.10.20.111 prod_k8s_worker01
10.10.20.112 prod_k8s_worker02
10.10.20.113 prod_k8s_worker03
10.10.20.114 prod_k8s_worker04
10.10.20.115 prod_k8s_worker05
10.10.20.116 prod_k8s_worker06
10.10.20.117 prod_k8s_worker07
10.10.20.118 prod_k8s_worker08
10.10.20.119 prod_k8s_worker09
10.10.20.120 prod_k8s_worker10
10.10.20.121 prod_k8s_worker11
10.10.20.122 prod_k8s_worker12
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
