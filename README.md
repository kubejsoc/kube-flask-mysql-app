# kube-flask-mysql-app

This is a Flask MySQL Demonstrator App.

This application have:

- Nginx 1.21.6
- uWSGI 2.0.19
- Python 3.10.2
- MySQL 5.6

We have the uWSGI configured to use `main.py` as the module in the `uwsgi.ini` file.

see:

```
[uwsgi]
#WSGI module and Callable app
virtualenv = /srv/app/public/venv/
chdir = /srv/app/public/
module = main
callable = app
plugins = python3
#User ID for nginx config
uid = www-data
#Group ID for nginx config
gid = www-data
#Serve as Master
master = true
#processes = Number of Process
processes = 3
#Socket path of WSGI
socket = :8001
#Listen on TCP socket
#http = 0.0.0.0:8080
#Modified Permissions
chmod-sock = 664
#Graceful reloading
lazy=true
#Auto cleanup the socket
vacuum = true
#For expected process signals at startup
die-on-term = true
```

## Start the Application

Clone the codebase to your local. We hope you will be having a Kubernetes development server in your local.
If yes, use the below command to start the kubernetes application:

```
git clone https://github.com/kubejsoc/kube-flask-mysql-app.git
cd kube-flask-mysql-app
kubectl apply -k k8s/
```

Please wait for a few minutes, then you may see:

```
debuggerboy@cassandra:~/labs/containers/playground/docker-uwsgi$ kubectl get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/flask-mysql-745fcf98fc-9xkbt   1/1     Running   0          34m
pod/web-67c55887cc-5bwjs           2/2     Running   0          34m

NAME                  TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/flask-mysql   ClusterIP      None           <none>        3306/TCP       34m
service/flask-nginx   LoadBalancer   10.96.221.94   10.88.2.200   80:31565/TCP   34m
service/flask-uwsgi   ClusterIP      None           <none>        8001/TCP       34m
service/kubernetes    ClusterIP      10.96.0.1      <none>        443/TCP        3h15m

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-mysql   1/1     1            1           34m
deployment.apps/web           1/1     1            1           34m

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-mysql-745fcf98fc   1         1         1       34m
replicaset.apps/web-67c55887cc           1         1         1       34m
```

I make use of MetalLB for assigning a IP Address for services (not included here).
