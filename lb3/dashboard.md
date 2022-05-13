[1]: https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/
[2]: https://www.howtoforge.com/tutorial/centos-kubernetes-docker-cluster/
[3]: https://download.docker.com/linux/centos/8/x86_64/stable/Packages/
[4]: https://github.com/flannel-io/flannel
[5]: https://www.weave.works/docs/net/latest/overview/
[6]: https://stackoverflow.com/questions/47845739/configuring-flannel-to-use-a-non-default-interface-in-kubernetes
[7]: http://linuxsoft.cern.ch/centos/8-stream/isos/x86_64/
[8]: https://www.centos.org/
[9]: https://github.com/kubernetes/dashboard/blob/master/docs/user/installation.md "Official Kubernets Dashboard"
[10]: https://www.rosehosting.com/blog/how-to-generate-a-self-signed-ssl-certificate-on-linux/ "Self-Signed Certificate"
[11]: https://www.tecmint.com/install-nfs-server-on-centos-8
[15]: https://www.jenkins.io/doc/book/installing/kubernetes/


[51]: https://kubernetes.io/docs/concepts/storage/volumes/
[52]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[70]: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/
[90]: https://kubernetes.io/docs/tasks/run-application/run-single-instance-stateful-application/

# Dashboard

## Installation

Das Dashboard soll vom lokalen Rechner her zugänglich sein. Für die Installation wird der [offiziellen Dokumentation][9] gefolgt.  Die nötigen Files werden unter `$HOME/dashboard` ablegen.

> wegen des Host-Only Netzwerkes wird das Dashboard nur vom lokalen Rechner her zugänglich sein

```
# mkdir $HOME/dashboard
# cd $HOME/dashboard
```

#### Dashboard Namespace

Namespace für das Dashboard definieren.

 ```
 # kubectl create namespace "kubernetes-dashboard"
 ```

 #### Dashbaord Secrets

Für das Dashboard muss das **secret**  `kubernetes-dashboard-certs` mit den vorgängig generierten Zertifikaten erstellt werden.

```
# kubectl create secret generic kubernetes-dashboard-certs --from-file=$HOME/certs -n kubernetes-dashboard
```

### Dashboard Konfiguration

Die zum Deployen des Dashboard nötige Definition in `$HOME/dashboard` unter `dashboard.yaml` speichern

```
# wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml -O dashboard.yaml
```

Das `dashboard.yaml` muss nun noch mit den vorgängig generierten Zertifikaten erweitert werden, ansonsten würden beim Deploy neue generiert werden.

Unter der *Deployment section* müssen die *self-signed* Zertifiakte als Argumente zur Pod Definition zugefügt werden.

```
 containers:
        - name: kubernetes-dashboard
          image: kubernetesui/dashboard:v2.2.0
          imagePullPolicy: Always
          ports:
            - containerPort: 8443
              protocol: TCP
          args:
            - --tls-cert-file=/tls.crt
            - --tls-key-file=/tls.key
            - --auto-generate-certificates
```

Das Argument `auto-generate-certificates` kann als Fallback bestehen bleiben.

### Dashboard deployen

Nun wird das *Dashboard* mit `kubectl create` deployed.

```
# kubectl create -f dashboard.yaml
```

> Testen mit `kubectl get deployment -n kubernetes-dashboard` und `kubectl get service -n kubernetes-dashboard`

```
# kubectl get deployment -n kubernetes-dashboard
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
dashboard-metrics-scraper   1/1     1            1           2m29s
kubernetes-dashboard        1/1     1            1           2m29s

# kubectl get service -n kubernetes-dashboard
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.96.11.76     <none>        8000/TCP   2m51s
kubernetes-dashboard        ClusterIP   10.101.189.71   <none>        443/TCP    2m51s
```

Umgekehrt könnte es mit `kubectl delete  -f dashboard.yaml` auch wieder removed werden.

Damit nun auch von ausserhalb des Node auf den *Service* zugegriffen werden kann, muss dieser *exposed* werden

> `--type=NodePort` ist nötig, da wir in einer *on-prem* Installation keinen *Load Balancer* haben

```
 # kubectl expose deployment kubernetes-dashboard --type=NodePort --name=dashboard-service -n kubernetes-dashboard
 ```

 Nun kann mit `kubectl get services -n kubernetes-dashboard`  der Port für den Dashboard Service abgefragt werden. In diesem Beispiel ist das Port 30251.

 ```
# kubectl get services -n kubernetes-dashboard
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
dashboard-metrics-scraper   ClusterIP   10.101.17.83   <none>        8000/TCP         7h23m
dashboard-service           NodePort    10.110.87.51   <none>        8443:30251/TCP   7h10m
kubernetes-dashboard        ClusterIP   10.97.78.22    <none>        443/TCP          7h23m
```

> Der Zugriff auf den Service Dashboard erfolgt unter https://192.168.11.11:30251



### Dashboard Admin Account

Damit über das Dashboard der Kubernetes Cluster tatsächlich administriert werden kann, muss ein Dashboard-Admin Account definiert werden. Dazu erstellen wir die Konfigurationsdatei `dashboard-admin.yaml` unter `$HOME/account`.

```
# mkdir $HOME/account
# cd $HOME/account
```

Account Definition `dashboard-admin.yaml`

```
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: dashboard-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: dashboard-admin
    namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: dashboard-admin
  namespace: kube-system
```

Und wiederum mit `kubectl` im Cluster erstellen.

```
#  kubectl create -f dashboard-admin.yaml
```

### Login Token

Mit dem YAML-File wird ein Secret erstellt, um anschliessen den Login Token für den `dashboard-admin`Account auszulesen.

```
# cat <<EOF>> secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: dashboard-admin-secret
  namespace: kube-system
  annotations:
    kubernetes.io/service-account.name: dashboard-admin
type: kubernetes.io/service-account-token
EOF

# kubectl create -f ./secret.yaml

# kubectl describe secret dashboard-admin-secret -n kube-system
```

Bequemer ist es, wenn dieses Kommando als *Bash-Script*  in `/usr/local/bin` abgelegt wird.

```
# cat <<EOF>> /usr/local/bin/token

#!/bin/bash
# listed dashboard-admin Token zum Einloggen im UI

kubectl describe secret dashboard-admin-secret -n kube-system
EOF
```

Skript ausführbar machen

```
# chmod 750 /usr/local/bin/token
```

Danach kann der Login-Token durch den Aufruf von `/usr/local/bin/token` abgefragt werden


Für den Dashboard Login unter https://192.168.11.11:30251 den ermittelten Token verwenden

> Der angegeben Port kann abweichend sein
