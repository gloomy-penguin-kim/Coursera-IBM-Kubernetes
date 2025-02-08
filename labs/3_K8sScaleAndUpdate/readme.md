cd 3_K8sScaleAndUpdate

export MY_NAMESPACE=sn-labs-$USERNAME

docker build -t us.icr.io/$MY_NAMESPACE/hello-world:1 . && docker push us.icr.io/$MY_NAMESPACE/hello-world:1

kubectl apply -f deployment.yaml

kubectl get pods

kubectl expose deployment/hello-world

in another tab... kubectl proxy

in the original tab... curl -L localhost:8001/api/v1/namespaces/sn-labs-$USERNAME/services/hello-world/proxy

kubectl scale deployment hello-world --replicas=3

for i in `seq 10`; do curl -L localhost:8001/api/v1/namespaces/sn-labs-$USERNAME/services/hello-world/proxy; done

kubectl scale deployment hello-world --replicas=1

kubectl get pods

update app.js

docker build -t us.icr.io/$MY_NAMESPACE/hello-world:2 . && docker push us.icr.io/$MY_NAMESPACE/hello-world:2

ibmcloud cr images

kubectl set image deployment/hello-world hello-world=us.icr.io/$MY_NAMESPACE/hello-world:2

kubectl rollout status deployment/hello-world

kubectl get deployments -o wide

curl -L localhost:8001/api/v1/namespaces/sn-labs-$USERNAME/services/hello-world/proxy

kubectl rollout undo deployment/hello-world

kubectl rollout status deployment/hello-world

kubectl get deployments -o wide

curl -L localhost:8001/api/v1/namespaces/sn-labs-$USERNAME/services/hello-world/proxy



kubectl create configmap app-config --from-literal=MESSAGE="This message came from a ConfigMap!"

edit deployment-configmap-env-var.yaml, update where it says <my_namespace> 

edit app.js to look like: 
app.get('/', function(req, res) {
    res.send(process.env.MESSAGE + '\n')
})

docker build -t us.icr.io/$MY_NAMESPACE/hello-world:3 . && docker push us.icr.io/$MY_NAMESPACE/hello-world:3

kubectl apply -f deployment-configmap-env-var.yaml

curl -L localhost:8001/api/v1/namespaces/sn-labs-$USERNAME/services/hello-world/proxy

kubectl delete configmap app-config && kubectl create configmap app-config --from-literal=MESSAGE="This message is different, and you didn't have to rebuild the image!"

kubectl rollout restart deployment hello-world

curl -L localhost:8001/api/v1/namespaces/sn-labs-$USERNAME/services/hello-world/proxy

