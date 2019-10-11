helm install --name my-release  --set rabbitmq.password=guest stable/rabbitmq

PS C:\> kubectl get services
NAME                           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                 AGE
details                        ClusterIP   10.110.165.24    <none>        9080/TCP                                4d5h
grafana-yaml-deployment        NodePort    10.98.52.86      <none>        3000:30857/TCP                          20h
kubernetes                     ClusterIP   10.96.0.1        <none>        443/TCP                                 4d12h
my-release-rabbitmq            ClusterIP   10.110.229.141   <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP   18h
my-release-rabbitmq-headless   ClusterIP   None             <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP   18h
productpage                    ClusterIP   10.97.123.119    <none>        9080/TCP                                4d5h
ratings                        ClusterIP   10.111.216.40    <none>        9080/TCP                                4d5h
reviews                        ClusterIP   10.109.244.28    <none>        9080/TCP                                4d5h

PS C:\> kubectl get pods
NAME                                       READY   STATUS    RESTARTS   AGE
details-v1-c5b5f496d-sgr6w                 2/2     Running   2          4d5h
grafana-test                               2/2     Running   2          3d
grafana-yaml-deployment-7f5c4cb5b9-v8sm9   2/2     Running   0          20h
grafana-yaml-deployment-7f5c4cb5b9-vv7h9   2/2     Running   0          20h
grafana-yaml-deployment-7f5c4cb5b9-x8hkd   2/2     Running   0          20h
kubernetes-bootcamp-b94cb9bff-vsprh        2/2     Running   2          3d3h
my-release-rabbitmq-0                      2/2     Running   1          18h
productpage-v1-c7765c886-6cpr9             2/2     Running   2          4d5h
ratings-v1-f745cf57b-87m7q                 2/2     Running   2          4d5h
reviews-v1-75b979578c-vmzn2                2/2     Running   2          4d5h
reviews-v2-597bf96c8f-plml7                2/2     Running   2          4d5h
reviews-v3-54c6c64795-x67ss                2/2     Running   2          4d5h

PS C:\> kubectl port-forward my-release-rabbitmq-0 15672:15672


0000000000000000000000000000000000000
PS C:\github\docker-rabbitmq-example\Kubernetes> kubectl apply -f .\publisher-deployment.yaml
deployment.apps/rabbitmq-publisher-deployment created

PS C:\github\docker-rabbitmq-example\Kubernetes> kubectl get deployment
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
details-v1                      1/1     1            1           4d9h
grafana-yaml-deployment         3/3     3            3           24h
kubernetes-bootcamp             1/1     1            1           3d7h
productpage-v1                  1/1     1            1           4d9h
rabbitmq-publisher-deployment   0/1     1            0           11s
ratings-v1                      1/1     1            1           4d9h
reviews-v1                      1/1     1            1           4d9h
reviews-v2                      1/1     1            1           4d9h
reviews-v3                      1/1     1            1           4d9h

PS C:\github\docker-rabbitmq-example\Kubernetes> kubectl get deployment
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
details-v1                      1/1     1            1           4d9h
grafana-yaml-deployment         3/3     3            3           24h
kubernetes-bootcamp             1/1     1            1           3d7h
productpage-v1                  1/1     1            1           4d9h
rabbitmq-publisher-deployment   0/1     1            0           19s
ratings-v1                      1/1     1            1           4d9h
reviews-v1                      1/1     1            1           4d9h
reviews-v2                      1/1     1            1           4d9h
reviews-v3                      1/1     1            1           4d9h

PS C:\github\docker-rabbitmq-example\Kubernetes> kubectl get pods
NAME                                             READY   STATUS             RESTARTS   AGE
details-v1-c5b5f496d-sgr6w                       2/2     Running            2          4d9h
grafana-test                                     2/2     Running            2          3d4h
grafana-yaml-deployment-7f5c4cb5b9-v8sm9         2/2     Running            0          24h
grafana-yaml-deployment-7f5c4cb5b9-vv7h9         2/2     Running            0          24h
grafana-yaml-deployment-7f5c4cb5b9-x8hkd         2/2     Running            0          24h
kubernetes-bootcamp-b94cb9bff-vsprh              2/2     Running            2          3d7h
my-release-rabbitmq-0                            2/2     Running            1          22h
productpage-v1-c7765c886-6cpr9                   2/2     Running            2          4d9h
rabbitmq-publisher-deployment-5fbf8ddff4-gbcsb   1/2     CrashLoopBackOff   1          28s
ratings-v1-f745cf57b-87m7q                       2/2     Running            2          4d9h
reviews-v1-75b979578c-vmzn2                      2/2     Running            2          4d9h
reviews-v2-597bf96c8f-plml7                      2/2     Running            2          4d9h
reviews-v3-54c6c64795-x67ss                      2/2     Running            2          4d9h

so check the error log

PS C:\github\docker-rabbitmq-example\Kubernetes> kubectl logs rabbitmq-publisher-deployment-5fbf8ddff4-gbcsb
Error from server (BadRequest): a container name must be specified for pod rabbitmq-publisher-deployment-5fbf8ddff4-gbcsb, choose one of: [publisher istio-proxy] or one of the init containers: [istio-init]

PS C:\github\docker-rabbitmq-example\Kubernetes> kubectl logs deployment/rabbitmq-publisher-deployment -c publisher
Traceback (most recent call last):
  File "/usr/src/app/publisher.py", line 381, in <module>
    main()
  File "/usr/src/app/publisher.py", line 374, in main
    amqp_url = os.environ['AMQP_URL']
  File "/usr/local/lib/python3.7/os.py", line 678, in __getitem__
    raise KeyError(key) from None
KeyError: 'AMQP_URL'

open rabbitmq admin
kubectl port-forward my-release-rabbitmq-0 15672:15672

check kiali for visual

kubectl scale deployment.v1.apps/rabbitmq-publisher-deployment --replicas=2

