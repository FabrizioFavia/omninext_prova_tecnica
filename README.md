
# AWS Node HTTP API Project

Questo progetto è un esempio di un'applicazione serverless AWS che utilizza il Serverless Framework per creare una API REST che consente di creare e ottenere informazioni sugli utenti. L'applicazione utilizza AWS Lambda, API Gateway e DynamoDB.

## Prerequisiti


1. **Creazione di un profilo IAM su Amazon AWS:** creare un profilo IAM valido su Amazon AWS con le autorizzazioni necessarie per creare risorse come Lambda, API Gateway e DynamoDB.

2. **Creazione di un profilo sul sito ufficiale di Serverless Framework:** Creare un account sul sito ufficiale di [Serverless Framework](https://www.serverless.com/) e configurare le tue credenziali (provider).

3. **Installazione di Serverless Framework:** Installare Serverless Framework globalmente utilizzando il comando npm:

    ```shell
    npm install -g serverless
    ```

4. **Connessione di Serverless Framework ad AWS:** Configurare Serverless Framework per utilizzare le credenziali AWS tramite il comando:

    ```shell
    serverless config credentials --profile: name --provider aws --key <AWS_ACCESS_KEY> --secret <AWS_SECRET_KEY>
    ```

## Configurazione

Il file `serverless.yml` contiene la configurazione del progetto, inclusi i servizi AWS utilizzati e le definizioni delle funzioni Lambda, come di seguito:

```yaml
org: captainfindus
app: aws-node-project
service: aws-node-http-api-project
frameworkVersion: '3'

provider:
  name: aws
  runtime: nodejs18.x
  lambdaHashingVersion: '20201221'
  environment:
    DYNAMODB_TABLE: ${self:service}-${sls:stage}
  httpApi:
    cors: true
  iam:
    role:
      statements:
        - Effect: Allow
          Action:
            - dynamodb:Query
            - dynamodb:Scan
            - dynamodb:GetItem
            - dynamodb:PutItem
            - dynamodb:UpdateItem
            - dynamodb:DeleteItem
          Resource: "arn:aws:dynamodb:${aws:region}:*:table/${self:provider.environment.DYNAMODB_TABLE}"

functions:
  create:
    handler: src/create.create
    events:
      - httpApi:
          path: /user
          method: post

  get:
    handler: src/get.get
    events:
      - httpApi:
          path: /user/{id}
          method: get
```

## Deploy

Per deployare il progetto su AWS, eseguire il seguente comando:

```shell
serverless deploy
```

## Configurazione dell'Ambiente AWS

Dopo aver deployato il progetto,  configurare l'ambiente AWS in modo da consentire l'accesso alle Lambda Functions tramite API Gateway:

1. **Creazione dell'API Gateway di tipo REST:** Assicurarsi di avere creato un'API Gateway di tipo REST su AWS, associata alle funzioni Lambda "GET" e "POST" definite nel file `serverless.yml`.

2. **Configurazione del Metodo GET:** Creare un metodo "GET" sull'API Gateway per la ricerca degli utenti, configurando la risorsa `{id}`.

3. **Configurazione del Metodo POST:** Crea un metodo "POST" sull'API Gateway per la creazione degli utenti.
