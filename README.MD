helm install my-kafka oci://registry-1.docker.io/bitnamicharts/kafka -f k8s/kafka/values.yaml 

### Install DB Postgres from Helm
helm install my-db oci://registry-1.docker.io/bitnamicharts/postgresql -f k8s/db/values.yaml

my-release > my-db

kubectl port-forward service/my-db-postgresql  5432:5432
kubectl port-forward service/my-kafka  9092:9092

From project root directory:
mvn package

docker image build -t alxinsh/docker-java-hw30-order:1.0.1 .
docker push alxinsh/docker-java-hw30-order:1.0.1

### Deploy service into K8s
```bash
kubectl apply -f ./k8s/
```

### Cleanup
kubectl delete -f ./k8s/
helm delete my-db
helm delete my-kafka
kubectl delete pvc data-my-db-postgresql-0