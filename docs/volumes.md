
docker volume create test-data

docker run --name counter -d \
  --mount type=volume,source=test-data,destination=/var/log/test \
  busybox \
  sh -c 'i=0; while true; do echo "$i: $(date)" >> /var/log/test/1.log; i=$((i+1)); sleep 1; done'

docker exec counter cat /var/log/test/1.log

docker run --name fluentd -d \
  --mount type=volume,source=test-data,destination=/var/log/input \
  --mount type=bind,source=/etc/fluentd/fluent.conf,destination=/fluentd/etc/fluent.conf \
  --mount type=bind,source=/etc/fluentd/output,destination=/var/log/output \
  --env FLUENTD_ARGS="-c /fluentd/etc/fluent.conf" \
  k8s.gcr.io/fluentd-gcp:1.30