4 applications:
order
payment
warehouse
delivery

Placing an order is carried out in the following sequence: order > payment > warehouse > delivery.
Saga pattern used.
Kafka is used for exchange between services.
Each service has a database in which the state is stored.

![sequence.drawio.png](..%2Fsequence.drawio.png)

To emulate runtime errors, the application processes part of the request correctly and the other part with errors.
The ordering application will fulfill all requests without errors.
The payment will be rejected if the order ID is divisible by 5.
Warehouse refusal order with ID, example: 6, 11, 16
Delivery rejection order with ID, example: 7, 12, 17

All order processing will be carried out as follows depending on the order ID:
Correct processing 1, 2, 3, 4, 8, 9, 13
Processing with errors 5, 6, 7, 10, 11, 12

### Install Kafka from Helm
helm install my-kafka oci://registry-1.docker.io/bitnamicharts/kafka -f k8s/kafka/values.yaml 

### Install DB Postgres from Helm
helm install my-db oci://registry-1.docker.io/bitnamicharts/postgresql -f k8s/db/values.yaml

### Debug. Port forwarding to local PC
```bash
kubectl port-forward service/my-db-postgresql  5432:5432
kubectl port-forward service/my-kafka  9092:9092 # doesn't work
```

### Deploy service into K8s
```bash
kubectl apply -f ./k8s/
```

### Enable Minikube tunnel
```bash
minikube tunnel
```

### Test applications
#### To execute order send POST request
```bash
curl -H 'Content-Type: application/json' \
-d '{ "orderDescription":"Order description", "productId":"123", "productPrice":"5.1", "productQuantity":"2.0", "deliveryAddress":"г.Москва, ул. Тверская, д.12"}' \
-X POST \
http://arch.homework/order
```

#### Get all Orders
```bash
curl http://arch.homework/order | json_pp
```

### Cleanup
kubectl delete -f ./k8s/
helm delete my-db
helm delete my-kafka
kubectl delete pvc data-my-db-postgresql-0