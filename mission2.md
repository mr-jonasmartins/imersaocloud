# Passos para implementação do Projeto - Parte 2

# Amazon Web Services

- Acessar a console da AWS. Na barra de pesquisas, digite IAM. Na seção Services, clique em IAM.
- Clique em Users e em seguida Add users, insira o nome **luxxy-covid-testing-system-pt-app1** e clique em Next para criar o usuário do tipo programmatic.

![Screenshot 2023-02-03 at 09.19.48.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/75dff1c8-1d2e-4bb8-a363-625667adf818/Screenshot_2023-02-03_at_09.19.48.png)

- Após avançar, em **Set permissions**, clique no botão Attach existing policies directly.

![Screenshot 2023-02-03 at 09.27.44.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/204df068-e2dd-47fb-b822-8c192d8890b2/Screenshot_2023-02-03_at_09.27.44.png)

- Digite **AmazonS3FullAccess** em **Search.**
- Selecione **AmazonS3FullAccess**

![Screenshot 2023-02-03 at 09.31.36.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0859de94-c411-407b-be71-8af94b6c34b9/Screenshot_2023-02-03_at_09.31.36.png)

- Clique em **Next**
- Revise todos os detalhes
- Clique em **Create user**

### Passos para fazer o download da chave de acesso

- Acesse o usuário **luxxy-covid-testing-system-pt-app1**
- Clique na aba **Security credentials**
- Navegue até a seção **Access keys**
- Clique em **Create access key**

![Screenshot 2023-02-03 at 09.48.38.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8264598b-1f83-4a51-8fbc-1a3f76f40210/Screenshot_2023-02-03_at_09.48.38.png)

- Selecione Command Line Interface (CLI) e **I understand the above recommendation and want to proceed to create an access key.**
- Clique em **Next**.
- Clique em **Create access key**
- Clique em **Download .csv file**
- Após o download finalizar, clique em Done.
- Com o download feito, renomeie o **.csv** para **luxxy-covid-testing-system-pt-app1.csv**

### Google Cloud Platform (GCP)

- Navegue até a Cloud SQL instance e crie um novo usuário **app** com a senha **welcome123456** no Cloud SQL MySQL database.
    
    [SQL User Creation_EDITED.mp4](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c80abf92-7467-436b-bee3-db25a88c70b4/SQL_User_Creation_EDITED.mp4)
    
- Se conecte ao Google Cloud Shell
- Faça o download dos arquivos da missão 2 **diretamente para o Cloud Shell usando o comando wget abaixo:**

```
cd ~
mkdir mission2_pt
cd mission2_pt
wget https://tcb-public-events.s3.amazonaws.com/icp/mission2.zip
unzip mission2.zip
```

- Verifique e copie o Public IP address de sua instância Cloud SQL em Overview.
- Conecte ao MySQL DB em execução no Cloud SQL (assim que aparecer a janela para colocar a senha, insira **welcome123456**)

```
mysql --host=<public_ip_cloudsql> --port=3306 -u app -p
```

- Após estar conectado ao banco de dados da instância, crie a tabela de produtos para testes.

```
use dbcovidtesting;
source ~/mission2/pt/db/create_table.sql
show tables;
exit;
```

- Habilite a Cloud Build API através do Cloud Shell.

```
# Comando para habilitar Cloud Build API

gcloud services enable cloudbuild.googleapis.com
```

**Known issue during this step**

```
ERROR: (gcloud.builds.submit) INVALID_ARGUMENT: could not resolve source: googleapi: Error 403: [989404026119@cloudbuild.gserviceaccount.com](mailto:989404026119@cloudbuild.gserviceaccount.com) does not have storage.objects.get access to the Google Cloud Storage object., forbidden

Para solucionar:

1. Acesse o IAM & Admin;
2. Clique no check-box Include Google-provided role grants 
3. Clique e selecione sua Cloud Build Service Account

Exemplo: 989404026119@cloudbuild.gserviceaccount.com Cloud Build Service Account

4. Na sua Cloud Build Service Account, do lado direito, clique em Edit principal
5. Clique em Add another role (Adicionar outra função);
6. Clique em Select Role, e filtre por Storage Admin ou gcs. Selecione Storage Admin (Full control of GCS resources).
7. Clique em Save and retorno para o Cloud Shell.
```

- Faça o Build da Docker image e suba para o Google Container Registry. Por gentileza, substitua o <PROJECT_ID> com o My First Project ID

```
cd ~/mission2_pt/mission2/pt/app
gcloud builds submit --tag gcr.io/<PROJECT_ID>/luxxy-covid-testing-system-app-pt
```

- Abra o Cloud Editor e edite o Kubernetes deployment file (**luxxy-covid-testing-system.yaml**) e atualize as variáveis abaixo na **linha 33** (em vermelho) com o seu <PROJECT_ID> no caminho da imagem Docker no Google Container Registry, na **linha 42** o AWS Bucket, AWS Keys (abrir o arquivo **luxxy-covid-testing-system-pt-app1.csv** e utilizar o **Access key ID** na **linha 44** e o **Secret access key** na **linha 46**) e o **IP Privado** do Cloud SQL Database na **linha 48**.

```
cd ~/mission2_pt/mission2/pt/kubernetes
luxxy-covid-testing-system.yaml

				image: gcr.io/**<PROJECT_ID**>/luxxy-covid-testing-system-app-pt:latest
...
				- name: AWS_BUCKET
          value: "**luxxy-covid-testing-system-pdf-pt-xxxx**"
        - name: S3_ACCESS_KEY
          value: "**xxxxxxxxxxxxxxxxxxxxx**"
        - name: S3_SECRET_ACCESS_KEY
          value: "**xxxxxxxxxxxxxxxxxxxx**"
        - name: DB_HOST_NAME
          value: "**172.21.0.3**"
```

- Se conecte ao GKE (Google Kubernetes Engine) cluster via Console (seguir video)
- Faça o Deploy da aplicação COVID-19 Testing Status System no Cluster

```
cd ~/mission2_pt/mission2/pt/kubernetes
kubectl apply -f luxxy-covid-testing-system.yaml
```

- Obtenha o IP Público e faça o teste da aplicação ([CLIQUE AQUI para baixar exemplo de Teste de COVID-19](https://tcb-public-events.s3.amazonaws.com/icp/mission2.zip)). Busque por **GKE**, clique em **Services & Ingress** e em seguida **Endpoints** endereço:porta
- Você deve visualizar a aplicação up & running! Congrats! 🎉

![Screen Shot 2022-02-07 at 4.26.27 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2f460137-7b68-438e-900d-56ccc3052651/Screen_Shot_2022-02-07_at_4.26.27_PM.png)
