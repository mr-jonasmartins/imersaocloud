# Passos para implementação do Projeto Hands-on - Parte 1

# Amazon Web Services (AWS)

- Acessar a console da AWS. Na barra de pesquisas, digite IAM. Na seção Services, clique em IAM.
- Clique em Users e em seguida Add users, insira o nome **terraform-pt-1** e clique em Next para criar o usuário do tipo programmatic.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a875ce20-3579-40eb-831c-66254b6a791a/Untitled.png)

- Após avançar, em **Set permissions**, clique no botão Attach existing policies directly.

![Screenshot 2023-02-03 at 09.27.44.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/204df068-e2dd-47fb-b822-8c192d8890b2/Screenshot_2023-02-03_at_09.27.44.png)

- Digite **AmazonS3FullAccess** em **Search.**
- Selecione **AmazonS3FullAccess**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6effb0e0-025f-4b74-818f-47e93de3db0b/Untitled.png)

- Clique em **Next**
- Revise todos os detalhes
- Clique em **Create user**

**[NEW] AWS has recently changed the way to create/download the access key. Follow the new steps:**

- Acesse o usuário **terraform-pt-1**
- Clique na aba **Security credentials**
- Navegue até a seção **Access keys**
- Clique em **Create access key**

![Screenshot 2023-02-03 at 09.48.38.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8264598b-1f83-4a51-8fbc-1a3f76f40210/Screenshot_2023-02-03_at_09.48.38.png)

- Selecione Command Line Interface (CLI) e **I understand the above recommendation and want to proceed to create an access key.**
- Clique em **Next**.
- Clique em **Create access key**
- Clique em **Download .csv file**
- Após o download finalizar, clique em Done.
- Com o download feito, renomeie o **.csv** para **accessKeys.csv**

# Google Cloud Platform (GCP)

- **[CLIQUE AQUI para baixar os arquivos do projeto hands-on.](https://tcb-public-events.s3.amazonaws.com/icp/mission1.zip)**
- Acessar a console da GCP e abrir o Cloud Shell
- Fazer o upload dos arquivos **accessKeys.csv** e **mission1.zip** para o Cloud Shell
- Checar se o upload dos arquivos foi concluído com sucesso usando o comando **ls -la**
- Após fazer o upload, executar os comandos de preparação dos arquivos:

```
mkdir mission1_pt
mv mission1.zip mission1_pt
cd mission1_pt
unzip mission1.zip
mv ~/accessKeys.csv mission1/pt
cd mission1/pt
chmod +x *.sh
```

- Execute os comandos abaixo para preparar o ambiente da AWS e GCP

```
./aws_set_credentials.sh accessKeys.csv
gcloud config set project <your-project-id>
```

- Clique em Autorize e execute o comando abaixo para setar o projeto no Google Cloud Shell

```
./gcp_set_project.sh
```

- Execute os comandos para habilitar as APIs do Kubernetes, Container Registry e Cloud SQL

```
gcloud services enable containerregistry.googleapis.com
gcloud services enable container.googleapis.com
gcloud services enable sqladmin.googleapis.com
```

**OBS IMPORTANTE (NÃO PULE ESTE PASSO):**

- **Antes de executar os comandos do terraform, abra o Google Cloud Editor e atualizar o arquivo tcb_aws_storage.tf substituindo o nome do bucket para um exclusivo (na AWS, os buckets precisam ter nomes únicos).**
    - Na **linha 4** do arquivo **tcb_aws_storage.tf**:
        - Abra o Google Cloud Editor
        - Substituir **xxxx** pelas iniciais do seu nome usando **5 letras** mais **5 números**:
        Exemplo: **luxxy-covid-testing-system-pdf-pt-jerod29292**
- Execute os seguintes comandos para provisionar os recursos de infraestrutura

```
cd ~/mission1_pt/mission1/pt/terraform/

terraform init
terraform plan
terraform apply
		digite **yes** para seguir
```

**Atenção**: O banco de dados Cloud SQL pode levar entre **15 a 25 minutos** para ser criado, sempre checar o **CloudShell** e clicar em **Reconectar** quando a sessão expirar (a sessão expira após **5 minutos** de inatividade por padrão)

- A mensagem de **warning** ao final da execução do **terraform apply** não é um problema, basta seguir em frente:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9594fd6c-e3f9-4316-8ff9-744f6f8e4fd7/Untitled.png)

<aside>
💡 Após concluir, acessar o [link](https://cloud.google.com/kubernetes-engine/docs/resources/autopilot-standard-feature-comparison) para "Comparar GKE Autopilot e Standard”.

</aside>

### [New] Configuração de Rede SQL

[SQL Networking Configuration_EDITED.mov](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6ab20977-4163-4519-8d2d-87fbfe5d5f8e/SQL_Networking_Configuration_EDITED.mov)

- Após a conclusão do provisionamento da instância do CloudSQL, acesse o serviço do Cloud SQL.
- Clique na sua instância do Cloud SQL.
- Na lateral esquerda, em Primary Instance, clique em **“Connections”.**
- Clicar na aba **Networking** e em **Instance IP assignment**, habilite o Private IP.
    - Em **Associated Network**, selecione “default”.
    - Clique em **Set up connection**
    - Enable **Service Networking API (se solicitar)**
    - Selecione **Use an automatically allocated IP range in your network**.
    - Clique em **Continue.**
    - Clique em **Create connection** e aguarde alguns minutos. Você verá a menssagem: “*Private services access connection for network default has been successfully created.”*
- Após finalizar, em **“Connections”**, **Autorized Networks**, clique em **"Adicionar Rede (Add Network)".**
    - Em **New Network**, insira as seguintes informações:
        - **Nome:** Public Access (Apenas para testes)
        - **Network:** 0.0.0.0/0
        - Clique em **Done**.
        - Clique em **Save** e aguarde finalizar a edição do Cloud SQL Instance.
        Essa atualização pode levar entre **10 a 20 minutos** para ser concluída

PS: Para ambientes de produção, é recomendado utilizar apenas a Rede Privada para o acesso ao banco de dados. 
⚠️ Nunca fornecer acesso à rede pública (0.0.0.0/0) para os banco de dados de produção.

**Chegando até aqui, você concluiu a implementação da primeira parte do Projeto Hands-on e fez a implementação dos recursos em múltiplos provedores de Cloud utilizando o Terrraform! Parabéns! 🚀🎉**

<aside>
💡 Faça o Download do Visual Studio Code utilizado pelo Jean durante a Imersão [AQUI](https://code.visualstudio.com/download)

</aside>
