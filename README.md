# cnv_cluster set up

### Copy ssh-key to nodes
```
ssh-copy-id it@prod-k8s-master01 &&
ssh-copy-id it@prod-k8s-master02 &&
ssh-copy-id it@prod-k8s-master03 &&
ssh-copy-id it@prod-k8s-ingress01 &&
ssh-copy-id it@prod-k8s-ingress02 &&
ssh-copy-id it@prod-k8s-ingress03 &&
ssh-copy-id it@prod-k8s-worker01 &&
ssh-copy-id it@prod-k8s-worker02 &&
ssh-copy-id it@prod-k8s-worker03 &&
ssh-copy-id it@prod-k8s-worker04 &&
ssh-copy-id it@prod-k8s-worker05 &&
ssh-copy-id it@prod-k8s-worker06 &&
ssh-copy-id it@prod-k8s-worker07 &&
ssh-copy-id it@prod-k8s-worker08 &&
ssh-copy-id it@prod-k8s-worker09 &&
ssh-copy-id it@prod-k8s-worker10 &&
ssh-copy-id it@prod-k8s-worker11 &&
ssh-copy-id it@prod-k8s-worker12 &&
ssh-copy-id it@prod-k8s-worker-tool
```

### Add host on main VM
```
cat <<EOF >> /etc/hosts
10.10.20.104 prod-k8s-master01
10.10.20.105 prod-k8s-master02
10.10.20.106 prod-k8s-master03

10.10.20.107 prod-k8s-ingress01
10.10.20.108 prod-k8s-ingress02
10.10.20.109 prod-k8s-ingress03

10.10.20.110 prod-k8s-worker-tool

10.10.20.111 prod-k8s-worker01
10.10.20.112 prod-k8s-worker02
10.10.20.113 prod-k8s-worker03
10.10.20.114 prod-k8s-worker04
10.10.20.115 prod-k8s-worker05
10.10.20.116 prod-k8s-worker06
10.10.20.117 prod-k8s-worker07
10.10.20.118 prod-k8s-worker08
10.10.20.119 prod-k8s-worker09
10.10.20.120 prod-k8s-worker10
10.10.20.121 prod-k8s-worker11
10.10.20.122 prod-k8s-worker12
EOF
```

### Add host on remote VM
```
#!/bin/bash
declare -a hosts=("worker01" "worker02" "worker03")
for i in "${hosts[@]}"
do
   ssh it@$i 'cat <<EOF >> /etc/hosts
10.10.20.104 prod-k8s-master01
10.10.20.105 prod-k8s-master02
10.10.20.106 prod-k8s-master03

10.10.20.107 prod-k8s-ingress01
10.10.20.108 prod-k8s-ingress02
10.10.20.109 prod-k8s-ingress03

10.10.20.110 prod-k8s-worker-tool

10.10.20.111 prod-k8s-worker01
10.10.20.112 prod-k8s-worker02
10.10.20.113 prod-k8s-worker03
10.10.20.114 prod-k8s-worker04
10.10.20.115 prod-k8s-worker05
10.10.20.116 prod-k8s-worker06
10.10.20.117 prod-k8s-worker07
10.10.20.118 prod-k8s-worker08
10.10.20.119 prod-k8s-worker09
10.10.20.120 prod-k8s-worker10
10.10.20.121 prod-k8s-worker11
10.10.20.122 prod-k8s-worker12
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
