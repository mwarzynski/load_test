# Load Tests: K6 deployment on Kubernetes


If you would like to use InfluxDB and Grafana, then you need to deploy them first.
```
kubectl apply -f grafana.yaml
kubectl apply -f influxdb.yaml
```

Then, we need to set up Grafana to show results from the correct Data Store.
```
kubectl port-forward grafana-random-characters 3000:3000
```
Default creds are `user=admin, pass=admin`.

Next thing is to add Data Source as InfluxDB (database will be 'k6').
And also you would need to import the dashboard: https://grafana.com/grafana/dashboards/2587

---

Then run the Load Test. Remember to change the `example.js` for your script.
If you don't want to save results to InfluxDB (and skipped setting it up), then just remove `--out` argument (with it's value).

```
INFLUX_IP=$(kubectl get pods -o wide | grep influxdb | awk '{print $6}')
kubectl run k6 --rm -i --image loadimpact/k6 -- run --out influxdb=http://${INFLUX_IP}:8086/k6 - < example.js
```
