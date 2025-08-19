minikube addons enable ingress
minikube ip


store.local   # 👈 custom host (you'll map it in /etc/hosts)

sudo nano /etc/hosts
192.168.49.2 store.local
