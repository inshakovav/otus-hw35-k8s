3 applications:
order
billing
notification

![sequence.drawio.png](..%2Fsequence.drawio.png)

### Install Kafka from Helm
helm install my-kafka oci://registry-1.docker.io/bitnamicharts/kafka -f kafka/values.yaml 

### Install DB Postgres from Helm
helm install my-db oci://registry-1.docker.io/bitnamicharts/postgresql -f db/values.yaml

### Debug. Port forwarding to local PC
```bash
kubectl port-forward service/my-db-postgresql  5432:5432
kubectl port-forward service/my-kafka  9092:9092 # doesn't work
```

### Deploy service into K8s
```bash
c
```

### Enable Minikube tunnel
```bash
kubectl get svc # Use svc list
kubectl expose deploy kuber-dp-billing-35
kubectl expose deploy kuber-dp-order-35
kubectl expose deploy kuber-dp-notification-35
```

### Test applications

#### Add an Account
```bash
curl -H 'Content-Type: application/json' \
-d '{ "name":"Василий Пупкин"}' \
-X POST \
arch.homework/account | json_pp
```

```bash
curl arch.homework/account | json_pp
```

#### Topup
```bash
curl -H 'Content-Type: application/json' \
-d '{ "amount":"20.2"}' \
-X POST \
arch.homework/account/top-up/1 | json_pp
```

```bash
curl http://arch.homework/account | json_pp
```

```bash
curl arch.homework/account | json_pp
```

### Post correct order
```bash
curl -H 'Content-Type: application/json' \
-d '{ "accountId":"1", "price":"1.1", "bookingAt":"2024-04-27T02:55:28.183+00:00" }' \
-X POST \
http://arch.homework/order | json_pp
```

### Post correct order with high price
```bash
curl -H 'Content-Type: application/json' \
-d '{ "accountId":"1", "price":"31.1", "bookingAt":"2024-04-27T02:55:28.183+00:00" }' \
-X POST \
http://arch.homework/order | json_pp
```

#### To execute order send POST request
Проверка идемпотентности. Если товар похожий уже создан, то данные в БД НЕ сохраняются.
Дубли ищутся по accountId, price, BookingAt - 5 минут (через 5 минут можно создать заказ на туже позицию).
```bash
curl -H 'Content-Type: application/json' \
-d '{ "bookingAt":"2024-04-28T02:55:28.183+00:00", "orderDescription":"Order description", "productId":"123", "productPrice":"5.1", "productQuantity":"2.3", "deliveryAddress":"г.Москва, ул. Тверская, д.12"}' \
-X POST \
http://arch.homework/order | json_pp
```

#### Get all Orders
```bash
curl http://arch.homework/order | json_pp
```

```bash
curl http://arch.homework/notification | json_pp
```

### Cleanup
kubectl delete -f .
helm delete my-db
helm delete my-kafka
kubectl delete pvc data-my-db-postgresql-0
kubectl delete pv ???
