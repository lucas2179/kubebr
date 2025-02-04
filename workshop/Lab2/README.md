# Lab 2: Escale e atualize Deployments

Neste lab, você irá aprender como atualizar o número de instâncias de um deployment e como disponibilizar uma atualização da sua aplicação em Kubernetes de forma segura.

Para este lab, você precisará de um deployment ativo da aplicação `guestbook`, do lab anterior. Caso você tenha apagado, recrie utilizando:

```console
$ kubectl run guestbook --image=ibmcom/guestbook:v1 -n <nome-do-namespace>
$ kubectl expose deployment guestbook --type="NodePort" --port=3000 -n <nome-do-namespace>
```
    
# 1. Escale aplicações utilizando réplicas

Uma *replica* é uma cópia de um pod que contém um serviço rodando. Tendo múltiplas replicas de um pod, você garante que seu deployment terá os recursos disponíveis para aguentar uma carga crescente na sua aplicação.

1. `kubectl` providencia um subcomando `scale` para mudar o tamanho de um deployment existente. Vamos aumentar nossa capacidade; de uma instância de`guestbook` para 3 instâncias:

   ``` console
   $ kubectl scale --replicas=3 deployment guestbook -n <nome-do-namespace>
   deployment "guestbook" scaled
   ```

   O Kubernetes agora irá tentar chegar ao estado desejado de 3 replicas, subindo 2 novos pods com a mesma configuração.

2. Para ver as mudanças acontecendo, você pode rodar:
   `kubectl rollout status deployment guestbook -n <nome-do-namespace>`.

   O rollout pode acontecer de forma tão rápida, que talvez as seguintes mensagens _não_ sejam exibidas:

   ```console
   $ kubectl rollout status deployment guestbook -n <nome-do-namespace>
   Waiting for rollout to finish: 1 of 3 updated replicas are available...
   Waiting for rollout to finish: 2 of 3 updated replicas are available...
   Waiting for rollout to finish: 3 of 3 updated replicas are available...
   deployment "guestbook" successfully rolled out
   ```

3. Uma vez finalizado o rollout, confira se os seus pods estão ativos usando o seguinte comando: 
   `kubectl get pods -n <nome-do-namespace>`.

   Você verá uma listagem com 3 réplicas do seu deployment:

   ```console
   $ kubectl get pods -n <nome-do-namespace>
   NAME                        READY     STATUS    RESTARTS   AGE
   guestbook-562211614-1tqm7   1/1       Running   0          1d
   guestbook-562211614-1zqn4   1/1       Running   0          2m
   guestbook-562211614-5htdz   1/1       Running   0          2m
   ```

**Tip:** Outra forma de melhorar a disponibilidade é
[adicionar clusters e regiões](https://console.bluemix.net/docs/containers/cs_planning.html#cs_planning_cluster_config)
ao seu deployment, como mostrado no seguinte diagrama:

![HA with more clusters and regions](../images/cluster_ha_roadmap.png)

# 2. Atualização e Roll Back de aplicações

O Kubernetes te permite atualizar uma aplicação sem a necessidade de que ela seja interrompida; isso facilita a mudança de um aplicativo já em execução e também permite desfazer uma atualização já feita, caso seja descoberto algum problema durante ou após a implementação.

No lab anterior, nós utilizamos uma imagem com a tag `v1`. Para a nossa atualização, nós vamos usar a imagem com a tag `v2`.

Para realizar o update e o roll back:   
1. Utilizando `kubectl`, você pode atualizar o seu deployment para que ele use a imagem
   `v2`. `kubectl` permite que você altere detalhes sobre recursos existentes, com o subcomando `set`. Podemos usá-lo para trocar a imagem que está sendo utilizada.
   
    ```$ kubectl set image deployment/guestbook guestbook=ibmcom/guestbook:v2 -n <nome-do-namespace>```

   Note que um Pod pode ter múltiplos containers, cada um com o seu próprio nome. Cada imagem pode ser trocada individualmente; ou todas de uma só vez, referenciando seu nome. No caso do nosso deployment `guestbook` Deployment, o nome do container também é `guestbook`.
   Múltiplos containers podem ser atualizados de uma só vez.
   ([Mais Informações](https://kubernetes.io/docs/user-guide/kubectl/kubectl_set_image/).)

3. Rode o comando  `kubectl rollout status deployment/guestbook -n <nome-do-namespace>` para checar o status do rollout. O rollout pode acontecer tão        rapidamente, que talvez as seguintes mensagens _não_ apareçam:
  
  
   ```console
   $ kubectl rollout status deployment/guestbook -n <nome-do-namespace>
   Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
   Waiting for rollout to finish: 3 out of 3 new replicas have been updated...
   Waiting for rollout to finish: 3 out of 3 new replicas have been updated...
   Waiting for rollout to finish: 3 out of 3 new replicas have been updated...
   Waiting for rollout to finish: 1 old replicas are pending termination...
   Waiting for rollout to finish: 1 old replicas are pending termination...
   Waiting for rollout to finish: 1 old replicas are pending termination...
   Waiting for rollout to finish: 3 of 3 updated replicas are available...
   Waiting for rollout to finish: 3 of 3 updated replicas are available...
   Waiting for rollout to finish: 3 of 3 updated replicas are available...
   deployment "guestbook" successfully rolled out
   ```

4. Teste a aplicação, como anteriormente, acessando `<public-IP>:<nodeport>` 
   no navegador, para confirmar que seu novo código está ativo.
   Lembre-se, para conseguir o "nodeport" e o "public-ip" utilize:

   `$ kubectl describe service guestbook -n <nome-do-namespace>`
   e
   `$ ibmcloud cs workers ikslab`

   Para certificar-se de que você está rodando a "v2" do guestbook, olhe no título da página; deverá ser exibido como `Guestbook - v2`

5. Caso você queira desfazer o seu último rollout, utilize:
   ```console
   $ kubectl rollout undo deployment guestbook -n <nome-do-namespace>
   deployment "guestbook"
   ```

   Você poderá então usar `kubectl rollout status deployment/guestbook -n <nome-do-namespace>` para ver o status.
   
6. Quando um rollout é feito, você vê referências à *old* replicas e *new* replicas.
   Como as *old* replicas são os 3 pods originais, implementados quando nós escalamos a aplicação. *new* replicas vêm dos pods criados recentemente, com a imagem diferente. Todos estes pods são pertencentes ao deployment. O deployment gerencia esses dois sets de pods, utilizando um recurso chamado ReplicaSet. Podemos observar os ReplicaSets do guestbook com:
   ```console
   $ kubectl get replicasets -l run=guestbook -n <nome-do-namespace>
   NAME                   DESIRED   CURRENT   READY     AGE
   guestbook-5f5548d4f     3        3        3        21m
   guestbook-768cc55c78    0        0        0         3h
   ```

Antes de continuarmos, vamos apagar a aplicação, e então nós Podemos aprender diferentes formas de chegar ao mesmo resultado:

Para remover o deployment, utilize `kubectl delete deployment guestbook -n <nome-do-namespace>`.

Para remover o serviço, utilize `kubectl delete service guestbook -n <nome-do-namespace>`.

**Parabéns!** Você testou neste Lab os conceitos de Escalabilidade, RollOut e RollBack do Kubernetes. O lab 2 está completo. 
Clique [aqui](../Lab3/README.md) para seguir para o próximo lab.
