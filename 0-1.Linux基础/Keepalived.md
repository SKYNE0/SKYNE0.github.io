! Configuration File for keepalived
global_defs {
    router_id k3s_100
}

vrrp_script k3s_healthy {
    script "/opt/rancher/scripts/k3s_healthy.sh"
    interval 5
    # weight 2
}

vrrp_instance VI_1 {
    state MASTER
    interface ens160
    virtual_router_id 50
    priority 100
    # nopreempt
    advert_int 3
    authentication {
        auth_type PASS
        auth_pass 1qaz!QAZ
}
    virtual_ipaddress {
        10.0.10.100/16
    }

    track_script {
        k3s_healthy
    }
}

#!/bin/bash
# @Author: SKYNE
# @Date: 2022.04.27
# @Desc: 用于keepalived的脚本

curl -i -k -m 3 https://127.0.0.1:6443
if [[ $? -eq 0 ]]; then
    exit 0
else
    systemctl restart docker
    systemctl restart k3s
    exit 1
fi