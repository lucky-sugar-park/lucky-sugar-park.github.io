```
#!/bin/bash

echo "Waiting for kafka connect to start listening on kafka-connect "
while : ; do
  curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors`
  echo -e `date` "Kafka connect listener HTTP state: " $curl_status " (waiting for 200)"
  if [ $curl_status -eq 200 ] ; then
    break;
  fi
  sleep 5
done


# localhost를 외부로 노출된 URL로 대채하거나, k8s cluster 내부라면 service.namespace로 대체할 수 있음
# 예시: http://kafka-connect-cp-kafka-connect.kafka:8083/connectors

echo -e "\n--\n+> Creating Kafka Connectors(s)"
# This shell script can be executed by k8s or docker run command
# 1. postgresql-to-mariadb-proj-source-connector
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/postgresql-to-mariadb-proj-source-connector/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @postgresql-to-mariadb-proj-source-connector.json
fi
echo "postgresql-to-mariadb-proj-source-connector is created"

sleep 1
# 2. postgresql-to-mariadb-proj-sink-connector
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/postgresql-to-mariadb-proj-sink-connector/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @postgresql-to-mariadb-proj-sink-connector.json
fi
echo "postgresql-to-mariadb-proj-sink-connector is created"

sleep 1
# 3. postgresql-to-mariadb-source-connector
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/postgresql-to-mariadb-source-connector/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @postgresql-to-mariadb-source-connector.json
fi
echo "postgresql-to-mariadb-source-connector is created"

sleep 1
# 4. postgresql-to-mariadb-sink-connector-conv-req
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/postgresql-to-mariadb-sink-connector-conv-req/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @postgresql-to-mariadb-sink-connector-conv-req.json
fi
echo "postgresql-to-mariadb-sink-connector-conv-req is created"

sleep 1
# 5. postgresql-to-mariadb-sink-connector-orig-data
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/postgresql-to-mariadb-sink-connector-orig-data/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @postgresql-to-mariadb-sink-connector-orig-data.json
fi
echo "postgresql-to-mariadb-sink-connector-conv-req is created"

sleep 1
# 6. mariadb-to-postgresql-source-connector
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/mariadb-to-postgresql-source-connector/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @mariadb-to-postgresql-source-connector.json
fi
echo "mariadb-to-postgresql-source-connector is created"

sleep 1
# 7. mariadb-to-postgresql-sink-connector
curl_status=`curl -s -o /dev/null -w %{http_code} http://localhost:8083/connectors/mariadb-to-postgresql-sink-connector/status`
if [ $curl_status -eq 404 ] then
  curl -X --location --request POST http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d @mariadb-to-postgresql-sink-connector.json
fi
echo "mariadb-to-postgresql-sink-connector is created"
```
