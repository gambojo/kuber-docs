# kuber commands

#### Вывод информации о кластере
```
$ kubectl cluster-info
```
#### Вывод списка узлов кластера
```
$ kubectl get nodes
```

#### Получение дополнительных сведений об объекте
```
$ kubectl describe node gke-kubia-85f6-node-0rrx
```

#### Вывод списка модулей
```
$ kubectl get pods
```

#### Создать контроллер репликации
```
$ kubectl create deployment nginx-deployment --image=nginx:latest --replicas=1
```

#### Создать service LoadBalancer для контроллера репликации
```
kubectl expose deployment nginx-deployment --type=LoadBalancer --port=8080 --target-port=80
```

#### Применить манифест
```
$ kubectl apply -f config.yaml
```

#### Просмотреть логи пода
```
$ kubectl logs <pod-name>
```

#### Зайти в контейнер
```
$ kubectl exec -it <pod-name> -- bash
```

#### Отображение IP-адреса и узла модуля при выводе списка модулей
```
$ kubectl get pods -o wide
```

#### Описание модуля
```
$ kubectl describe pod kubia-hczji
```

#### Доступ к панели управления
```
$ kubectl cluster-info | grep dashboard
```

#### Вывод списка подов с адресами и нодами
```
$ kubectl get pods -o wide
```



#### Вывод информации по использованию
```
$ kubectl explain pods
$ kubectl explain pod.spec
```

#### Проброс хостового порта 8888 на порт пода 80
```
$ kubectl port-forward nginx-test 8888:80
```


```
$ kubectl get po --show-labels
$ kubectl get po -L creation_method,env (стр 111)
$ kubectl label po kubia-manual creation_method=manual
$ kubectl label po kubia-manual-v2 env=debug --overwrite
```

```
$ kubectl get po -l env
$ kubectl get po -l '!env'
```

```
$ kubectl annotate pod kubia-manual mycompany.com/someannotation="foo bar"
```

```
alias kcd='kubectl config set-context
$(kubectl config currentcontext) --namespace namespace-name'
```

```
$ kubectl delete po --all
kubectl delete all --all
```

```
$ kubectl logs mypod --previous
```

```
$ kubectl delete rc kubia --cascade=false
```

#### Template
```
$ kubectl run pod my-pod --namespace default --image=nginx --dry-run=client -o yaml > pod.yml
```

#### Get events
```
$ kubectl get events -o wide
```

#### Get logs
```
$ kubectl logs -n kube-system -s kustom-scheduler
```
