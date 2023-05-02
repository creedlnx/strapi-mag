Para o *build* e execução deste projeto, usei a *stack* de Docker, Docker Hub, Minikube, Helm e Strapi

Caso não tenha estas *stacks*, abaixo segue a documentação de cada uma destas:

Docker: https://docs.docker.com/

Docker Hub: https://docs.docker.com/docker-hub/

Minikube: https://minikube.sigs.k8s.io/docs/start/

Helm: https://helm.sh/docs/intro/install/

Strapi CLI: https://docs.strapi.io/dev-docs/installation/cli

Strapi Deploy: https://docs.strapi.io/dev-docs/deployment

Considerando que o ambiente com ***minikube*** ou até mesmo o *cluster* de ***kubernetes***  e a stack usada para o *deploy* já estão prontos e com o contexto correto no *kubernetes* selecionado, para aplicar o *deploy* executar os *steps* a seguir:

Fazer o clone deste repositório e acessá-lo.

Estando dentro do diretório clonado, executar o comando

    helm install strapi strapi-chart/

A saída deste comando deverá ser esta:

    ❯ helm install strapi strapi-chart/
    Release "strapi" has been installed. Happy Helming!
    NAME: strapi
    LAST DEPLOYED: Tue May  2 12:29:32 2023
    NAMESPACE: default
    STATUS: deployed
    REVISION: 2
    NOTES:
    1. Get the application URL by running these commands:
      export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services strapi)
      export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
      echo http://$NODE_IP:$NODE_PORT

O projeto irá subir no *kubernetes* no *namespace default*, para validar, executar

    kubectl get all -n default

Esta deverá ser a saída:

    NAME                         READY   STATUS    RESTARTS   AGE
    pod/strapi-d48c78889-dr2gk   1/1     Running   0          17m
    
    NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          15h
    service/strapi       NodePort    10.106.109.45   <none>        1337:32202/TCP   17m
    
    NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/strapi   1/1     1            1           17m
    
    NAME                               DESIRED   CURRENT   READY   AGE
    replicaset.apps/strapi-d48c78889   1         1         1       17m

A porta configurada para este serviço é a porta padrão do strapi; 1337. Para expor esta porta afim de utilizar a aplicação executar o comando a seguir:

    kubectl port-forward service/strapi 1337:http

Caso esta porta já esteja em uso trocar a porta no `strapi-chart/values.yaml` linha 20

    service:
        type: NodePort
        port: 1337

