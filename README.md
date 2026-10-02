Hi! Welcome to the K8s readme. 
1. Operator is running in its own namespace
Test:
kubectl get pods -n elastic-system
Expected:
elastic-operator-0   1/1     Running
2. Elasticsearch has 3 nodes, each on a different worker node
Test
kubectl get pods -n logging -o wide
Expected:
livs-pods-es-default-0            1/1     Running  
livs-pods-es-default-1            1/1     Running  
livs-pods-es-default-2            1/1     Running  
3. Each Elasticsearch node has a persistent volume
Test:
kubectl get pvc -n logging
Expected:
elasticsearch-data-livs-pods-es-default-0 Bound
elasticsearch-data-livs-pods-es-default-1 Bound
elasticsearch-data-livs-pods-es-default-2 Bound
Test:
kubectl get storageclass
Expected: 
local-path (default)   rancher.io/local-path  
4. Cluster health is green
Test:
curl -s localhost:9200/_cluster/health
Expected:
"status" : "green"
5. Kibana reachable through HTTPS
Test:
curl -k https://kibana.example.com
Expected:
302 redirect to /login
6. Elastic password is NOT stored in any manifest
Test:
kubectl -n logging get secret livs-pods-es-elastic-user -o jsonpath='{.data.elastic}' | base64 -d
Expected:
Base64 password stored in Kubernetes Secret
7. Only Kibana can reach Elasticsearch
Test:
kubectl get networkpolicy -n logging
Expected:
es-logs-allow-kibana-operator   elasticsearch.k8s.elastic.co/cluster-name=es-logs   27h
Show failed connection:
Test:
kubectl run test --rm -it --image=busybox --restart=Never \
  -- wget -qO- http://es-default.logging.svc:9200
Expected:
wget: can't connect
8. Every pod has resource requests and limits
Manifest
resources:
  requests:
    cpu: "500m"
    memory: "2Gi"
  limits:
    cpu: "1"
    memory: "4Gi"
JVM heap sets memory limit. 
9. Cluster survives the loss of one worker node
Test:
kubectl drain <node> --ignore-daemonsets --delete-emptydir-data
Watch cluster:
kubectl get pods -n logging -w
Expected:
•	Pods reschedule to remaining nodes
•	Cluster returns to green
Restore node:
kubectl uncordon <node>

