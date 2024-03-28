1. Setup kafka on docker

2. Create k3d cluster on the same network

```
k3d cluster create kumakafka --network external-service-with-kafka-protocol_default
```

3. Check IPs

4. Put the correct ip in docker-compose:

```
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://172.24.0.3:29092
```

5. run consumer / producer

```
echo 'apiVersion: v1
kind: Namespace
metadata:
  name: kafka' | kubectl apply -f -
```

```
kubectl -n kafka run kafka-producer -ti --image=strimzi/kafka:0.17.0-kafka-2.4.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --broker-list 172.24.0.3:29092 --topic my-topic
```

```
kubectl -n kafka run kafka-consumer -ti --image=strimzi/kafka:0.17.0-kafka-2.4.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server 172.24.0.3:29092 --topic my-topic --from-beginning
```

6. Install kuma 2.5.4

7. Patch namespace 

```
echo 'apiVersion: v1
kind: Namespace
metadata:
  name: kafka
  labels:
   kuma.io/sidecar-injection: enabled' | kubectl apply -f -
```

8. Restart consumer / producer

It works.

9. Use external service

10. Restart consumer/producer using the mesh address

```
kubectl -n kafka run kafka-consumer -ti --image=strimzi/kafka:0.17.0-kafka-2.4.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server kafka1.mesh:80 --topic my-topic --from-beginning
```



