# Passos para implementação do Projeto - Parte 3

# Google Cloud Platform - Passos para Migração do Banco de Dados MySQL

- Conectar ao Google Cloud Shell
- Download o dump do banco de dados

```
cd ~
mkdir mission3_pt
cd mission3_pt
wget https://tcb-public-events.s3.amazonaws.com/icp/mission3.zip
unzip mission3.zip
```

- Conectar ao banco de dados MySQL no Cloud SQL. Senha: **welcome123456**

```
mysql --host=<public_ip_address> --port=3306 -u app -p
```

- Importar o dump do banco de dados no Cloud SQL

```
use dbcovidtesting;
source ~/mission3_pt/mission3/pt/db/db_dump.sql
```

- Checar se os dados foram importados com sucesso

```
select * from records;
exit;
```

# Amazon Web Services - Passos para a Migração dos arquivos PDF

- Conectar no AWS Cloud Shell
- Download dos arquivos PDF (Comprovante de teste negativo escaneado em PDF)

```
mkdir mission3_pt
cd mission3_pt
wget https://tcb-public-events.s3.amazonaws.com/icp/mission3.zip
unzip mission3.zip
```

- Sincronizar os arquivos PDF com o seu bucket criado no AWS S3 usado para o COVID-19 Testing Status System. **Altere o nome do bucket para o seu bucket.**

```
cd mission3/pt/pdf_files
aws s3 sync . s3://luxxy-covid-testing-system-pdf-pt-xxxx
```

- Testar a aplicação. Ao testar a aplicação e navegar na opção "Ver registros" você deverá ser capaz de visualizar os dados importados!

![Screen Shot 2022-02-07 at 4.39.51 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c710178-ec07-4aa5-8259-cec6e3484820/Screen_Shot_2022-02-07_at_4.39.51_PM.png)

**Parabéns! Você migrou uma aplicação e seu banco de dados do "on-premises" para uma Arquitetura MultiCloud!**
