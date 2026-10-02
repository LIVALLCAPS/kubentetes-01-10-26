1. Install ECK Operator
bash
kubectl apply -f https://download.elastic.co/downloads/eck/2.11.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/2.11.0/operator.yaml
2. Apply Elasticsearch
bash
kubectl apply -f manifests/elasticsearch.yaml
3. Apply Kibana
bash
kubectl apply -f manifests/kibana.yaml
4. Check Cluster Health
bash
kubectl get pods -n logging
kubectl get pvc -n logging
kubectl get storageclass
kubectl get csidrivers
