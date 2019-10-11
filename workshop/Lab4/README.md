# 1. Verifique a saúde dos apps

O Kubernetes usa a verificação de disponibilidade (**liveness probes**) para saber quando é o momento de reiniciar um container. Por exemplo, as liveness probes podem descobrir um deadlock; onde uma aplicação está rodando, porém está impossibilitada de fazer algum progresso. Reiniciar o container em tal estado pode ajudar a tornar a aplicação mais disponível apesar dos erros.

Além disso, o Kubernetes usa **readiness probes** para saber quando um container está pronto para começar a aceitar tráfego. Um pod é considerado pronto quando todos os seus containers estiverem prontos. Uma utilidade para essa verificação é controlar quais pods são usados como back-end para serviços. Quando um pod não está pronto, ele é removido dos balanceadores de carga.

Existem 3 métodos para identificar se o pod está vivo (liveness probe) ou se ele está pronto (readiness probe):
- **HTTP**

	O probe envia uma requisicão HTTP para o pod, caso essa resposta esteja entre o range 200-400, ele se encontra em seu estado normal
   
- **Command**

   Você executa um comando dentro do container, se o retorno do status for 0, o pod se encontra em seu estado normal
   
- **TCP**

   A probe estabelesse uma conexão TCP com o pod, se a conexão for realizada, o pod se encontra em seu estado normal
Para este lab, devemos clonar o repositorio kube101.  
`git clone https://github.com/itirohidaka/kube101.git`. Após clonar, você poderá encontrar os arquivos
Nesse exemplo, definimos uma liveness probe Command para checar a saúde do container.

 Abra o arquivo  `liveness-exec.yml` com um editor de textos. Esse script de configuração cria um pod, que cria um arquivo chamado `healthy`, aguarda 30 segundos, deleta esse arquivo e aguardar mais 600 segundos

Veja:  
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
       command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```


Então o liveness probe irá executar um cat no arquivo criado nesse yaml. Nos primeiros 30 segundos, toda vez que a probe for executada ela retornará que aquele pod está vivo, após esses 30 segundos quando arquivo é apagado, o probe irá entender que aquele pod possui uma falha e vai reiniciá-lo.

Executando:

   ```
   kubectl create -f liveness-exec.yml -n <nome-do-namespace>
   ```
   
   Após 30 segundos, vamos verificar se o pod foi reiniciado:



   ```
   kubectl get pods -n <nome-do-namespace>
   ```


   Você pode checar na coluna de Restarts, que aquele pod possui um restart, e provavelmente está rodando novamente.

Clique [aqui](../LabDevOps/README.md) para seguir para o próximo lab.
