

- Criar bucket (controle de acesso uniforme)

- criar conta de serviço (adm do storage)

- baixar keyfile.json e codificar base64 (cat keyfile.json | base64 -w 0)

- adicionar gem: gem "google-cloud-storage"

- configurar storage type em cada ambiente (config>enviroments>development;production)
           config.active_storage.service = :google

* Se usar variável de ambiente no Dockerfile (desaconselhavel)

- Adicionar variável de ambiente GOOGLE_CREDENTIALS com valor igual a keyfile em base64 no Dockerfile 

- configurar storage file:

google:
  service: GCS
  project: rails-lab-264600
  credentials: <%= Base64.decode64(ENV['GOOGLE_CREDENTIALS']) %>
  bucket: rails-gcs



* Se utilizar docker secret e docker compose

docker secret create my_secret ./keyfile.json


version: '3.1'
services:
    db:
        image: rails-lab
        restart: always
        environment:
            GOOGLE_CREDENTIALS /run/secrets/my_secret_gcp
        secrets:
           - my_secret_gcp
           
No projeto 

google:
  service: GCS
  project: rails-lab-264600
  credentials: <%= ENV['GOOGLE_CREDENTIALS'] %>
  bucket: rails-gcs

* Se utilizar Kubernetes incluir como uma variável secretRef

envFrom:
 - secretRef:
     name: storage-secret
              
Codificar em base64 e inclui no yaml secret.

Para recuperar no arquivo de config do storage do projeto, não é necessário decodificar (base64). O kubernetes já entrega a aplicação decodificado:

google:
  service: GCS
  project: rails-lab-264600
  credentials: <%= ENV['GOOGLE_CREDENTIALS'] %>
  bucket: rails-gcs




