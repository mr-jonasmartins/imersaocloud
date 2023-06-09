# Kubernetes com Cloud na Prática

# Passos

- Download e upload o arquivo tcb-vote.zip file to Cloud Shell:
https://www.dropbox.com/s/x0jvi2hr7fgbu4p/tcb-vote-pt.zip?dl=0
- Unzip tcb-vote.zip
mkdir kube
mv tcb-vote*.zip kube
cd kube
unzip tcb-vote*.zip
- Set project

```
gcloud config set project <PROJECT_ID>

```

- Build e push a imagem para o Container Registry

```
gcloud services enable cloudbuild.googleapis.com --project <PROJECT_ID>
gcloud builds submit --tag gcr.io/<PROJECT_ID>/tcb-vote-front

```

- Crie um cluster Kubernetes (GKE) na Google Cloud e conecte-se a ele
- Deploy a app no GKE

```
kubectl apply -f tcb-vote-plus-redis.yaml

```

- Habilite o autoscaling - horizontal pod autoscaling

```
kubectl autoscale deployment tcb-vote-front --cpu-percent=50 --min=1 --max=10
kubectl get hpa
kubectl get pods
kubectl get deployment tcb-vote-front

```

- Simule acesso de usuários (aumentar load)

```
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- <http://tcb-vote-front>; done"

```

- Acompanhe o autoscale

```
kubectl get hpa tcb-vote-front --watch
kubectl get deployment tcb-vote-front

```

- Pare o load
Aperte CTRL +C na aba do cloud shell rodando o load generator
- Acompanhe o scale back
