ALIBABA_CLOUD_ACCESS_KEY_ID=
ALIBABA_CLOUD_ACCESS_KEY_SECRET=
ALIBABA_CLOUD_OSS_ENDPOINT=

oss://k8s-bk/

https://help.aliyun.com/document_detail/40654.html

BUCKET=k8s-bk
REGION=cn-shanghai

velero install \
  --provider alibabacloud \
  --image registry.$REGION.aliyuncs.com/acs/velero:1.4.2-2b9dce65-aliyun \
  --bucket $BUCKET \
  --secret-file ./credentials-velero \
  --use-volume-snapshots=false \
  --backup-location-config region=$REGION \
  --use-restic \
  --plugins registry.$REGION.aliyuncs.com/acs/velero-plugin-alibabacloud:v1.0.0-2d33b89 \
  --kubeconfig=/root/.kube/old-web \
  --wait

kubectl delete namespace/velero clusterrolebinding/velero
kubectl delete crds -l component=velero


velero backup --kubeconfig=/root/.kube/old-web  create web-ops --include-namespaces web-ops --wait

velero restore create --kubeconfig=/root/.kube/config --from-backup web-ops --wait

velero backup describe web-ops

velero backup logs web-ops

velero backup --kubeconfig=/root/.kube/old-web --default-volumes-to-restic create devops --include-namespaces devops --wait
velero restore create --kubeconfig=/root/.kube/config --from-backup devops --wait


