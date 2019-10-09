# Lab 1. Prepare e faça o deploy da sua primeira aplicação

Aprenda como fazer o deploy de uma aplicação em um cluster Kubernetes dentro do IBM Kubernetes Service

# 0. Como provisionar um cluster Kubernetes

Na IBM Cloud, podemos provisionar um cluster Kubernetes rapidamente. Para testar, podemos provisionar um cluster Lite, que é a versão gratuita, porém, neste lab, será utilizado um cluster que será fornecido para vocês. 
1. Instale as CLIs da IBM Cloud e faça o login, como descrito no  [Lab 0](../Lab0/README.md).
2. Provisionando um cluster na IBM Cloud:

   ```$ ibmcloud ks cluster-create --name <name-of-cluster>```

3. Utilize o comando abaixo para verificar o status da criação do cluster (somente exemplo):

   ```console
   $ ibmcloud ks clusters
   Name        ID                  State       Created        Workers   Location   Version       
   itirofree   0a582a8553e33a5f0   requested   1 minute ago   1         hou02      1.11.7_1543 
   ```

# 1. Deploy da sua primeira aplicação
Primeiramente, a CLI do kubernetes `kubectl` precisa ser configurada para conversar com o cluster provisionado.

1. Execute `$ ibmcloud ks cluster-config <name-of-cluster>`, e configure a variável de ambiente `KUBECONFIG` baseado na saída do comando. Isso fará seu client `kubectl` apontar para seu cluster Kubernetes.

Após feita a configuração, precisamos criar um novo namespace no cluster fornecido. Um Namespace é uma espécie de divisão do cluster, e este será o seu ambiente neste lab. Para isso, execute `$ kubectl create namespace <nome-do-namespace>`.

Nessa parte do lab nós faremos o deploy de uma aplicação chamada `guestbook` no namespace que criado.

1. Execute o `guestbook` rodando o seguinte comando:

   ```$ kubectl run guestbook --image=ibmcom/guestbook:v1 -n <nome-do-namespace>```

   Esse comando pode levar um tempo. Para checar o status da aplicação em execução, 
você pode rodar  `$ kubectl get pods -n <nome-do-namespace>`.

   Você irá se deparar com uma saída parecida com essa:

   ```console
   $ kubectl get pods -n <nome-do-namespace>
   NAME                          READY     STATUS              RESTARTS   AGE
   guestbook-59bd679fdc-bxdg7    0/1       ContainerCreating   0          1m
   ```
   Após um tempo o status deverá estar como `Running`.
   
   ```console
   $ kubectl get pods -n <nome-do-namespace>
   NAME                          READY     STATUS              RESTARTS   AGE
   guestbook-59bd679fdc-bxdg7    1/1       Running             0          1m
   ```
   
   O resultado final não é somente um pod contendo nossos containers da aplicação, 
mas também o recurso chamado Deployment que gerencia o ciclo de vida desses pods.
 
   
3. Assim que os status estiverem como `Running`, nós precisamos expor esse Deployment
   como um serviço para que consigamos acessá-lo através do IP do worker node.
   A aplicação `guestbook` atende na porta 3000.  Execute:

   ```console
   $ kubectl expose deployment guestbook --type="NodePort" --port=3000 -n <nome-do-namespace>
   service "guestbook" exposed
   ```

4. Para encontrar a porta usada nesse worker node, examine seu novo serviço: 

   ```console
   $ kubectl get service guestbook -n <nome-do-namespace>
   NAME        TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
   guestbook   NodePort   10.10.10.253   <none>        3000:31208/TCP   1m
   ```
   
   Podemos ver que nossa `<nodeport>` é a `31208`. Nós podemos verificar também o mapeamento da porta 3000
   dentro do pod exposto para o cluster na porta 31208. Essa porta no range 31000 é escolhida automaticamente, 
   e pode ser diferente da sua.

5. Agora, a aplicação `guestbook` está rodando no seu cluster e exposta para internet. Precisamos descobrir como acessá-la.
   O worker node que está rodando no kubernetes service pega um endereço IP externo.
   Execute o comando `$ ibmcloud ks workers <name-of-cluster>`, e note que o IP público é listado na linha `<public-IP>`.
   
   ```console
   $ ibmcloud ks workers <name-of-cluster>
   OK
   ID                                                 Public IP        Private IP     Machine Type   State    Status   Zone    Version  
   kube-hou02-pa1e3ee39f549640aebea69a444f51fe55-w1   173.193.99.136   10.76.194.30   free           normal   Ready    hou02   1.5.6_1500*
   ```
   
   Podemos ver que nosso `<public-IP>` é `173.193.99.136`.
   
6. Agora que você possui o IP e a porta, você pode acessar a aplicação no seu browser com seguinte endereço
  `<public-IP>:<nodeport>`. Nesse exemplo seria `173.193.99.136:31208`.
   
Parabéns, você fez o deploy de uma aplicação no Kubernetes!

Quando você concluir os passos acima, você pode também usar esse deployment
[próximo lab deste curso](../Lab2/README.md), ou você pode remover o deployment e encerrar o curso por aqui

  1. Para remover o deployment, execute `$ kubectl delete deployment guestbook -n <nome-do-namespace>`.

  2. Para remover o serviço, execute  `$ kubectl delete service guestbook -n <nome-do-namespace>`.

Agora você voltar na raiz do repositório para se preparar para o próximo Lab: `$ cd ..`.
