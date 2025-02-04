# Lab 0: IBM Cloud Kubernetes Service


Antes de começar o seu aprendizado, você precisará instalar as CLIs necessárias para criar e gerenciar seu cluster Kubernetes no IBM Cloud Kubernetes Service, e também para implementar apps encapsulados no seu cluster.

Neste lab, você encontrará informações para a instalação das seguintes CLIs e plug-ins:

* IBM Cloud CLI, Versão 0.5.0 ou posterior
* IBM Cloud Kubernetes Service plug-in
* Kubernetes CLI, Versão 1.10.8 ou posterior

Caso tenha as CLIs e plug-ins já instalados, você pode pular esta etapa e seguir para a próxima.

# Instalando a IBM Cloud command-line interface

1. Como pré-requisito para o plug-in IBM Cloud Container Service, instale a [IBM Cloud command-line interface](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started). Uma vez instalada, você pode acessar a IBM Cloud a partir da sua command-line utilizando o prefixo `ibmcloud`.
2. Para realizar o login na IBM Cloud CLI: `ibmcloud login`.
3. Insira sua credencial da IBM Cloud. Nesse ponto o instrutor já deve te-lo convidado para fazer parte da conta IBM Cloud do evento

   **Note:** Se você possui um ID federado, use `ibmcloud login --sso` para realizar o login na IBM Cloud CLI. Insira seu nome, e utilize a URL disponibilizada na sua CLI para adquirir uma senha de utilização única. Você sabe que utiliza um ID federado no momento em que o login falha sem o parâmetro `--sso` e funciona normalmente com o `--sso`.

# Instalando o IBM Cloud Kubernetes Service plug-in
1. Para criar um cluster Kubernetes e gerenciar worker nodes, instale o IBM Cloud Container Service plug-in:
   ```ibmcloud plugin install container-service```

   **Note:** O prefixo para rodar commandos utilizando o IBM Cloud Container Service plug-in é `ibmcloud ks`.

2. Para verificar se a instalação do plug-in foi feita corretamente, rode o seguinte comando:
```ibmcloud plugin list```

   IBM Cloud Container Service plug-in será exibido nos resultados como `container-service`.

# Download da Kubernetes CLI

Para ter uma versão local do Kubernetes dashboard e para implementar apps em seu cluster, você precisará instalar a Kubernetes CLI correspondente ao seu sistema operacional:

* [OS X](https://storage.googleapis.com/kubernetes-release/release/v1.10.8/bin/darwin/amd64/kubectl)
* [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.10.8/bin/linux/amd64/kubectl)
* [Windows](https://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/windows/amd64/kubectl.exe)

https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows

**Para usuários Windows:** Instale a Kubernetes CLI no mesmo diretório que a IBM Cloud CLI. Essa configuração facilita algumas mudanças em caminhos de arquivo quando você roda alguns comandos posteriormente.

**Para usuários OS X e Linux:**

1. Mova o arquivo executável para o diretório `/usr/local/bin` usando o comando `mv /<path_to_file>/kubectl /usr/local/bin/kubectl` .

2. Certifique-se que o caminho `/usr/local/bin` esteja listado em sua variável de sistema PATH.
```
$echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

3. Converta o arquivo binário para um arquivo executável:  `chmod +x /usr/local/bin/kubectl`

# Efetuando o Login (Terminal)
Para efetuar o login usando a linha de comando, siga os passos abaixo:

**Para usuários OS X e Linux:**
1. Abra o Terminal

2. Para efetuar o login:
```
ibmcloud login
```

3. Digite seu usuário (e-mail) e senha.

4. Selecione a conta (1986766 - IBM), ou se tiver uma única conta cadastrada passe para o próximo passo.

5. Selecione a região. Selecione a região "us-south ou Dallas" para que tenha a possibilidade de criar um cluster gratuito.

# Download do Código-fonte
O repositório `guestbook` contém a aplicação que iremos implementar, e iremos utilizar os arquivos de configuração deste repositório. A aplicação Guestbook tem duas versões; v1 e v2, que iremos utilizar para demonstrar algumas funcionalidades posteriormente. Todos os aquivos de configuração que usamos estão no diretório guestbook/v1.


O repositório `Guestbook` contém o codigo que voce ira usar durante os laboratorios
```console
$ git clone https://github.com/itirohidaka/guestbook.git
```
