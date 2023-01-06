


imgs=(
    longhornio/longhorn-engine:v1.2.3
    longhornio/longhorn-manager:v1.2.3
    longhornio/longhorn-ui:v1.2.3
    longhornio/longhorn-instance-manager:v1_20211210
    longhornio/longhorn-share-manager:v1_20211020
    longhornio/backing-image-manager:v2_20210820
    longhornio/csi-attacher:v3.2.1
    longhornio/csi-provisioner:v2.1.2
    longhornio/csi-node-driver-registrar:v2.3.0
    longhornio/csi-resizer:v1.2.0
    longhornio/csi-snapshotter:v3.0.3
)
for img in $imgs; do
  docker pull $img
done

 helm install  -n prometheus prometheus prometheus

 helm list  --all-namespaces



 ## 

 echo "InitiatorName=$(/sbin/iscsi-iname)" > /etc/iscsi/initiatorname.iscsi
systemctl restart iscsid

 - https://github.com/longhorn/longhorn/issues/2319
 - iscsid: can't open InitiatorAlias configuration file /etc/iscsi/initiatorname.iscsi
 - iscsiadm: Cannot perform discovery. Invalid Initiatorname.\\niscsiadm: Could not perform SendTargets discovery: invalid parameter\\n, error exit status 7\""