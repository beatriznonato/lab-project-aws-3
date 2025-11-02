# 🛠️ Guia Completo de AWS CLI, SDKs e CodeDeploy

> Documentação detalhada sobre AWS CLI, SDKs para desenvolvimento e automação de deploys com CodeDeploy

[![AWS](https://img.shields.io/badge/AWS-Cloud-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![CLI](https://img.shields.io/badge/CLI-Tools-blue?style=for-the-badge&logo=gnu-bash)](https://aws.amazon.com/cli/)
[![SDK](https://img.shields.io/badge/SDK-Development-green?style=for-the-badge&logo=javascript)](https://aws.amazon.com/tools/)

## 📑 Índice

- [AWS CLI (Command Line Interface)](#-aws-cli)
- [AWS SDKs (Software Development Kits)](#-aws-sdks)
- [AWS CodeDeploy](#-aws-codedeploy)

---

## 💻 AWS CLI (Command Line Interface)

### O que é AWS CLI?

AWS CLI é uma **ferramenta unificada de linha de comando** para gerenciar seus serviços AWS. Com apenas uma ferramenta para baixar e configurar, você pode controlar múltiplos serviços AWS diretamente do terminal.

### Versões Disponíveis

#### AWS CLI v1
```
📦 Python-based
🔧 Requer Python 2.7+ ou 3.4+
📅 Manutenção apenas (legado)
```

#### AWS CLI v2 (Recomendado)
```
⚡ Standalone binary
🚀 Melhor performance
✨ Novos recursos
🎯 Recomendado para novos projetos
```

### Instalação

#### Linux/macOS
```bash
# AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Verificar instalação
aws --version
```

#### Windows
```powershell
# Download do instalador MSI
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi

# Verificar instalação
aws --version
```

#### Via Package Managers
```bash
# macOS (Homebrew)
brew install awscli

# Ubuntu/Debian
sudo apt install awscli

# CentOS/RHEL
sudo yum install awscli
```

### Configuração Inicial

#### Método 1: aws configure

```bash
aws configure

# Responda as perguntas:
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json
```

**Arquivos criados:**
```
~/.aws/credentials    → Credenciais
~/.aws/config        → Configurações
```

#### Método 2: Variáveis de Ambiente

```bash
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-east-1
```

#### Método 3: IAM Role (EC2)

Para instâncias EC2, use **IAM Instance Profile** - sem credenciais hardcoded!

### Profiles (Perfis)

Gerencie **múltiplas contas** ou **ambientes** facilmente.

**Criar profiles:**
```bash
aws configure --profile dev
aws configure --profile prod
```

**Usar profiles:**
```bash
# Comando específico
aws s3 ls --profile dev

# Definir profile padrão para sessão
export AWS_PROFILE=dev
aws s3 ls
```

**Arquivo ~/.aws/credentials:**
```ini
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

[dev]
aws_access_key_id = AKIAI44QH8DHBEXAMPLE
aws_secret_access_key = je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY

[prod]
aws_access_key_id = AKIAI44QH8DHCEXAMPLE
aws_secret_access_key = xje7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
```

### Formato de Comando

```bash
aws <service> <operation> [options]
```

**Exemplos:**
```bash
aws ec2 describe-instances
aws s3 cp file.txt s3://bucket/
aws lambda invoke --function-name MyFunction output.json
```

### Output Formats

**Formatos disponíveis:**
- `json` (padrão) - Fácil de parsear
- `yaml` - Mais legível
- `text` - Para scripts
- `table` - Visualização tabular

```bash
# JSON (padrão)
aws ec2 describe-instances

# YAML
aws ec2 describe-instances --output yaml

# Table
aws ec2 describe-instances --output table

# Text
aws ec2 describe-instances --output text
```

### Filtering e Querying

#### JMESPath Query

Extraia dados específicos com `--query`.

```bash
# Listar apenas IDs de instâncias
aws ec2 describe-instances --query 'Reservations[*].Instances[*].InstanceId'

# Instâncias com tags específicas
aws ec2 describe-instances --query 'Reservations[*].Instances[?Tags[?Key==`Name` && Value==`WebServer`]]'

# Output customizado
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress]' --output table
```

#### Filtros

Use `--filters` para filtrar antes do query.

```bash
# Instâncias running
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running"

# Por tag
aws ec2 describe-instances --filters "Name=tag:Environment,Values=production"

# Por tipo de instância
aws ec2 describe-instances --filters "Name=instance-type,Values=t3.micro"
```

### Comandos Essenciais por Serviço

#### EC2

```bash
# Listar instâncias
aws ec2 describe-instances

# Criar instância
aws ec2 run-instances \
  --image-id ami-0c55b159cbfafe1f0 \
  --instance-type t3.micro \
  --key-name MyKeyPair \
  --security-group-ids sg-0123456789abcdef0 \
  --subnet-id subnet-0123456789abcdef0

# Parar instância
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Iniciar instância
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Terminar instância
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Criar snapshot
aws ec2 create-snapshot --volume-id vol-0123456789abcdef0 --description "Backup"
```

#### S3

```bash
# Listar buckets
aws s3 ls

# Listar objetos em bucket
aws s3 ls s3://my-bucket/

# Upload arquivo
aws s3 cp file.txt s3://my-bucket/

# Upload diretório (recursivo)
aws s3 cp ./folder s3://my-bucket/folder/ --recursive

# Download arquivo
aws s3 cp s3://my-bucket/file.txt ./

# Sync (sincronizar)
aws s3 sync ./local-folder s3://my-bucket/remote-folder/

# Deletar arquivo
aws s3 rm s3://my-bucket/file.txt

# Deletar bucket (vazio)
aws s3 rb s3://my-bucket

# Deletar bucket com conteúdo
aws s3 rb s3://my-bucket --force
```

#### Lambda

```bash
# Listar funções
aws lambda list-functions

# Invocar função (síncrono)
aws lambda invoke \
  --function-name MyFunction \
  --payload '{"key":"value"}' \
  response.json

# Invocar assíncrono
aws lambda invoke \
  --function-name MyFunction \
  --invocation-type Event \
  --payload '{"key":"value"}' \
  response.json

# Atualizar código da função
aws lambda update-function-code \
  --function-name MyFunction \
  --zip-file fileb://function.zip

# Ver logs
aws logs tail /aws/lambda/MyFunction --follow
```

#### IAM

```bash
# Listar usuários
aws iam list-users

# Criar usuário
aws iam create-user --user-name alice

# Criar access key
aws iam create-access-key --user-name alice

# Anexar policy
aws iam attach-user-policy \
  --user-name alice \
  --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess

# Listar roles
aws iam list-roles

# Criar grupo
aws iam create-group --group-name Developers

# Adicionar usuário ao grupo
aws iam add-user-to-group --user-name alice --group-name Developers
```

#### DynamoDB

```bash
# Listar tabelas
aws dynamodb list-tables

# Criar tabela
aws dynamodb create-table \
  --table-name Users \
  --attribute-definitions AttributeName=UserId,AttributeType=S \
  --key-schema AttributeName=UserId,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST

# Inserir item
aws dynamodb put-item \
  --table-name Users \
  --item '{"UserId": {"S": "user123"}, "Name": {"S": "Alice"}}'

# Buscar item
aws dynamodb get-item \
  --table-name Users \
  --key '{"UserId": {"S": "user123"}}'

# Scan (ler toda tabela)
aws dynamodb scan --table-name Users
```

### AWS CLI com Scripts

#### Bash Script Example

```bash
#!/bin/bash

# Stop all EC2 instances with tag Environment=dev
INSTANCE_IDS=$(aws ec2 describe-instances \
  --filters "Name=tag:Environment,Values=dev" "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text)

if [ -n "$INSTANCE_IDS" ]; then
  echo "Stopping instances: $INSTANCE_IDS"
  aws ec2 stop-instances --instance-ids $INSTANCE_IDS
else
  echo "No running dev instances found"
fi
```

#### Python with subprocess

```python
import subprocess
import json

# Execute CLI command
result = subprocess.run(
    ['aws', 'ec2', 'describe-instances', '--output', 'json'],
    capture_output=True,
    text=True
)

# Parse JSON output
data = json.loads(result.stdout)

# Process results
for reservation in data['Reservations']:
    for instance in reservation['Instances']:
        print(f"Instance ID: {instance['InstanceId']}")
        print(f"State: {instance['State']['Name']}")
```

### Paginação

Muitos comandos retornam resultados paginados.

```bash
# Paginar automaticamente (padrão desde CLI v2)
aws s3api list-objects-v2 --bucket my-large-bucket

# Controlar manualmente
aws s3api list-objects-v2 --bucket my-bucket --max-items 100

# Usar token de continuação
aws s3api list-objects-v2 --bucket my-bucket --starting-token <token>
```

### Dry Run

Teste comandos sem executá-los.

```bash
# Testar sem executar
aws ec2 run-instances \
  --dry-run \
  --image-id ami-0c55b159cbfafe1f0 \
  --instance-type t3.micro
```

### AWS CLI Aliases

Crie **atalhos** para comandos frequentes.

**~/.aws/cli/alias:**
```ini
[toplevel]

whoami = sts get-caller-identity

running-instances = ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,PublicIpAddress,Tags[?Key==`Name`].Value|[0]]' \
  --output table

list-buckets = s3 ls
```

**Usar aliases:**
```bash
aws whoami
aws running-instances
```

### AWS CloudShell

**Terminal AWS no browser** com CLI pré-instalado e autenticado.

**Características:**
- ✅ 1 GB de armazenamento persistente por região
- ✅ Pré-autenticado com suas credenciais
- ✅ Ferramentas pré-instaladas (aws-cli, python, node, etc)
- ✅ Acessível via console AWS

### Boas Práticas CLI

- 🔐 **Nunca hardcode credenciais** - use profiles ou IAM roles
- 🔄 **Rotacione access keys** regularmente
- 📝 **Use --dry-run** antes de comandos destrutivos
- 🎯 **Use --query** para otimizar output
- 📊 **Use --output table** para melhor visualização
- 🔍 **Combine com jq** para parsing avançado
- 💾 **Use aliases** para comandos frequentes
- ⚠️ **Cuidado com --force** e comandos de deleção

### Troubleshooting

```bash
# Debug mode (verbose)
aws s3 ls --debug

# Ver versão
aws --version

# Verificar configuração
aws configure list

# Ver perfil atual
aws sts get-caller-identity

# Limpar cache de credenciais
rm -rf ~/.aws/cli/cache/
```

---

## 🔧 AWS SDKs (Software Development Kits)

### O que são AWS SDKs?

AWS SDKs são **bibliotecas** que facilitam a interação programática com serviços AWS em diversas linguagens de programação.

### SDKs Disponíveis

| Linguagem | SDK | Package Manager |
|-----------|-----|-----------------|
| **Python** | Boto3 | pip |
| **JavaScript** | AWS SDK for JavaScript | npm |
| **Java** | AWS SDK for Java | Maven/Gradle |
| **.NET** | AWS SDK for .NET | NuGet |
| **PHP** | AWS SDK for PHP | Composer |
| **Ruby** | AWS SDK for Ruby | gem |
| **Go** | AWS SDK for Go | go get |
| **C++** | AWS SDK for C++ | - |
| **Rust** | AWS SDK for Rust | cargo |

### Python - Boto3

#### Instalação

```bash
pip install boto3
```

#### Configuração

Boto3 usa as **mesmas credenciais** do AWS CLI:
- `~/.aws/credentials`
- Variáveis de ambiente
- IAM roles (EC2/Lambda)

#### Cliente vs Resource

**Client (Low-level):**
```python
import boto3

# Client API - baixo nível
client = boto3.client('s3')
response = client.list_buckets()
print(response)
```

**Resource (High-level):**
```python
import boto3

# Resource API - alto nível, mais pythonic
s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)
```

#### Exemplos Práticos

**S3 Operations:**
```python
import boto3

s3 = boto3.client('s3')

# Upload arquivo
s3.upload_file('local.txt', 'my-bucket', 'remote.txt')

# Download arquivo
s3.download_file('my-bucket', 'remote.txt', 'local.txt')

# Listar objetos
response = s3.list_objects_v2(Bucket='my-bucket')
for obj in response['Contents']:
    print(obj['Key'])

# Deletar objeto
s3.delete_object(Bucket='my-bucket', Key='file.txt')

# Upload com metadata
s3.put_object(
    Bucket='my-bucket',
    Key='file.txt',
    Body=b'Hello World',
    Metadata={'author': 'alice'}
)
```

**DynamoDB Operations:**
```python
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Users')

# Put item
table.put_item(
    Item={
        'UserId': 'user123',
        'Name': 'Alice',
        'Email': 'alice@example.com'
    }
)

# Get item
response = table.get_item(Key={'UserId': 'user123'})
item = response['Item']
print(item)

# Update item
table.update_item(
    Key={'UserId': 'user123'},
    UpdateExpression='SET Email = :email',
    ExpressionAttributeValues={':email': 'newemail@example.com'}
)

# Query
response = table.query(
    KeyConditionExpression='UserId = :uid',
    ExpressionAttributeValues={':uid': 'user123'}
)

# Scan (toda tabela)
response = table.scan()
items = response['Items']
```

**Lambda Invocation:**
```python
import boto3
import json

lambda_client = boto3.client('lambda')

# Invocar função
response = lambda_client.invoke(
    FunctionName='MyFunction',
    InvocationType='RequestResponse',  # Síncrono
    Payload=json.dumps({'key': 'value'})
)

# Ler resposta
result = json.loads(response['Payload'].read())
print(result)
```

**EC2 Operations:**
```python
import boto3

ec2 = boto3.resource('ec2')

# Criar instância
instances = ec2.create_instances(
    ImageId='ami-0c55b159cbfafe1f0',
    InstanceType='t3.micro',
    MinCount=1,
    MaxCount=1,
    KeyName='MyKeyPair',
    SecurityGroupIds=['sg-0123456789abcdef0'],
    TagSpecifications=[
        {
            'ResourceType': 'instance',
            'Tags': [
                {'Key': 'Name', 'Value': 'MyWebServer'},
                {'Key': 'Environment', 'Value': 'dev'}
            ]
        }
    ]
)

# Listar instâncias
for instance in ec2.instances.filter(Filters=[{'Name': 'instance-state-name', 'Values': ['running']}]):
    print(f"{instance.id}: {instance.instance_type}")

# Parar instância
instance = ec2.Instance('i-1234567890abcdef0')
instance.stop()
```

**SNS Publish:**
```python
import boto3

sns = boto3.client('sns')

# Publicar mensagem
response = sns.publish(
    TopicArn='arn:aws:sns:us-east-1:123456789012:my-topic',
    Message='Hello from Python!',
    Subject='Test Message'
)

print(f"MessageId: {response['MessageId']}")
```

**SQS Operations:**
```python
import boto3

sqs = boto3.client('sqs')
queue_url = 'https://sqs.us-east-1.amazonaws.com/123456789012/my-queue'

# Enviar mensagem
sqs.send_message(
    QueueUrl=queue_url,
    MessageBody='Hello from Python!'
)

# Receber mensagens
response = sqs.receive_message(
    QueueUrl=queue_url,
    MaxNumberOfMessages=10,
    WaitTimeSeconds=20  # Long polling
)

for message in response.get('Messages', []):
    print(message['Body'])
    
    # Deletar após processar
    sqs.delete_message(
        QueueUrl=queue_url,
        ReceiptHandle=message['ReceiptHandle']
    )
```

#### Error Handling

```python
import boto3
from botocore.exceptions import ClientError

s3 = boto3.client('s3')

try:
    s3.head_bucket(Bucket='my-bucket')
    print("Bucket exists")
except ClientError as e:
    error_code = e.response['Error']['Code']
    if error_code == '404':
        print("Bucket does not exist")
    elif error_code == '403':
        print("Access denied")
    else:
        raise
```

### JavaScript - AWS SDK

#### Instalação

```bash
# SDK v3 (modular, recomendado)
npm install @aws-sdk/client-s3
npm install @aws-sdk/client-dynamodb
npm install @aws-sdk/client-lambda

# SDK v2 (legacy)
npm install aws-sdk
```

#### Configuração

```javascript
// SDK v3
import { S3Client } from "@aws-sdk/client-s3";

const s3Client = new S3Client({ 
  region: "us-east-1",
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
  }
});
```

#### Exemplos (SDK v3)

**S3 Operations:**
```javascript
import { S3Client, ListBucketsCommand, PutObjectCommand, GetObjectCommand } from "@aws-sdk/client-s3";

const s3 = new S3Client({ region: "us-east-1" });

// Listar buckets
const listBuckets = async () => {
  const command = new ListBucketsCommand({});
  const response = await s3.send(command);
  console.log(response.Buckets);
};

// Upload arquivo
const uploadFile = async () => {
  const command = new PutObjectCommand({
    Bucket: "my-bucket",
    Key: "file.txt",
    Body: "Hello World"
  });
  await s3.send(command);
};

// Download arquivo
const downloadFile = async () => {
  const command = new GetObjectCommand({
    Bucket: "my-bucket",
    Key: "file.txt"
  });
  const response = await s3.send(command);
  const body = await response.Body.transformToString();
  console.log(body);
};
```

**DynamoDB Operations:**
```javascript
import { DynamoDBClient, PutItemCommand, GetItemCommand } from "@aws-sdk/client-dynamodb";

const dynamodb = new DynamoDBClient({ region: "us-east-1" });

// Put item
const putItem = async () => {
  const command = new PutItemCommand({
    TableName: "Users",
    Item: {
      UserId: { S: "user123" },
      Name: { S: "Alice" }
    }
  });
  await dynamodb.send(command);
};

// Get item
const getItem = async () => {
  const command = new GetItemCommand({
    TableName: "Users",
    Key: {
      UserId: { S: "user123" }
    }
  });
  const response = await dynamodb.send(command);
  console.log(response.Item);
};
```

**Lambda Invocation:**
```javascript
import { LambdaClient, InvokeCommand } from "@aws-sdk/client-lambda";

const lambda = new LambdaClient({ region: "us-east-1" });

const invokeFunction = async () => {
  const command = new InvokeCommand({
    FunctionName: "MyFunction",
    Payload: JSON.stringify({ key: "value" })
  });
  
  const response = await lambda.send(command);
  const result = JSON.parse(Buffer.from(response.Payload).toString());
  console.log(result);
};
```

### Java - AWS SDK

#### Dependency (Maven)

```xml
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>s3</artifactId>
    <version>2.20.0</version>
</dependency>
```

#### Exemplo S3

```java
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.*;
import software.amazon.awssdk.core.sync.RequestBody;

public class S3Example {
    public static void main(String[] args) {
        S3Client s3 = S3Client.builder()
            .region(Region.US_EAST_1)
            .build();
        
        // Upload arquivo
        PutObjectRequest request = PutObjectRequest.builder()
            .bucket("my-bucket")
            .key("file.txt")
            .build();
        
        s3.putObject(request, RequestBody.fromString("Hello World"));
        
        // Listar buckets
        ListBucketsResponse response = s3.listBuckets();
        response.buckets().forEach(bucket -> {
            System.out.println(bucket.name());
        });
    }
}
```

### Boas Práticas SDKs

- 🔐 **Nunca hardcode credenciais** no código
- 🌍 **Use variáveis de ambiente** ou IAM roles
- ⚡ **Reuse clients** - são thread-safe
- 🔄 **Implemente retry logic** adequado
- ⏱️ **Configure timeouts** apropriados
- 📝 **Log requests** para debugging
- 🎯 **Use SDK específico** por serviço (v3 JS)
- 🔒 **Valide inputs** antes de enviar

---

## 🚀 AWS CodeDeploy

### O que é CodeDeploy?

AWS CodeDeploy é um serviço de **deployment automatizado** que facilita o deploy de aplicações para instâncias EC2, funções Lambda, serviços ECS e servidores on-premises.

### Por que usar CodeDeploy?

```
✅ Automação completa de deploys
✅ Minimize downtime com deploys Blue/Green
✅ Rollback automático em caso de falha
✅ Deploy para múltiplos ambientes
✅ Integração com CI/CD (CodePipeline, Jenkins, GitHub)
✅ Monitoramento de health durante deploy
```

### Componentes Principais

#### 1. Application

**Container lógico** que agrupa recursos de deployment.

```
Application: MyWebApp
  ├── Deployment Group: Production
  ├── Deployment Group: Staging
  └── Deployment Group: Development
```

#### 2. Deployment Group

**Conjunto de instâncias/recursos** onde a aplicação será deployada.

**Configurações:**
- 🎯 Instâncias EC2 (por tags, ASG, ou manual)
- ⚙️ Deployment configuration
- 🔔 Triggers (SNS notifications)
- 📊 CloudWatch alarms
- 🔄 Rollback settings

#### 3. Revision

**Versão da aplicação** a ser deployada.

**Pode ser:**
- 📦 Arquivo ZIP/TAR em S3
- 📂 Repositório GitHub
- 🐳 Imagem Docker (para ECS/Lambda)

#### 4. Deployment Configuration

**Define como o deploy acontece.**

**Opções pré-definidas:**

| Configuração | Descrição |
|--------------|-----------|
| `CodeDeployDefault.OneAtATime` | Deploy uma instância por vez |
| `CodeDeployDefault.HalfAtATime` | Deploy em 50% das instâncias |
| `CodeDeployDefault.AllAtOnce` | Deploy em todas simultaneamente |

**Custom:** Defina porcentagens específicas

### Plataformas Suportadas

#### 1. EC2/On-Premises

Deploy para instâncias EC2 ou servidores on-premises.

**Requisitos:**
- CodeDeploy Agent instalado
- IAM role apropriado
- Acesso à internet ou VPC endpoints

#### 2. AWS Lambda

Deploy de novas versões de funções Lambda.

**Estratégias:**
- Canary
- Linear  
- All-at-once

#### 3. Amazon ECS

Deploy de serviços containerizados no ECS.

**Estratégias:**
- Blue/Green deployment

### AppSpec File

**Arquivo de configuração** que define como o deploy será executado.

#### AppSpec para EC2/On-Premises (YAML)

```yaml
version: 0.0
os: linux

files:
  - source: /
    destination: /var/www/html

permissions:
  - object: /var/www/html
    owner: apache
    group: apache
    mode: 755
    type:
      - directory
  - object: /var/www/html/*
    owner: apache
    group: apache
    mode: 644
    type:
      - file

hooks:
  ApplicationStop:
    - location: scripts/stop_application.sh
      timeout: 300
      runas: root
  
  BeforeInstall:
    - location: scripts/before_install.sh
      timeout: 300
      runas: root
  
  AfterInstall:
    - location: scripts/after_install.sh
      timeout: 300
      runas: root
  
  ApplicationStart:
    - location: scripts/start_application.sh
      timeout: 300
      runas: root
  
  ValidateService:
    - location: scripts/validate_service.sh
      timeout: 300
      runas: root
```

#### Lifecycle Event Hooks (Ordem)

```
1. ApplicationStop
   ↓
2. DownloadBundle
   ↓
3. BeforeInstall
   ↓
4. Install
   ↓
5. AfterInstall
   ↓
6. ApplicationStart
   ↓
7. ValidateService
```

#### AppSpec para Lambda (YAML)

```yaml
version: 0.0
Resources:
  - MyFunction:
      Type: AWS::Lambda::Function
      Properties:
        Name: MyLambdaFunction
        Alias: live
        CurrentVersion: 1
        TargetVersion: 2

Hooks:
  - BeforeAllowTraffic: PreTrafficHook
  - AfterAllowTraffic: PostTrafficHook
```

#### AppSpec para ECS (YAML)

```yaml
version: 0.0
Resources:
  - TargetService:
      Type: AWS::ECS::Service
      Properties:
        TaskDefinition: "arn:aws:ecs:us-east-1:123456789012:task-definition/my-task:2"
        LoadBalancerInfo:
          ContainerName: "my-container"
          ContainerPort: 80

Hooks:
  - BeforeInstall: "LambdaFunctionToValidateBeforeInstall"
  - AfterInstall: "LambdaFunctionToValidateAfterInstall"
  - AfterAllowTestTraffic: "LambdaFunctionToValidateAfterTestTrafficStarts"
  - BeforeAllowTraffic: "LambdaFunctionToValidateBeforeTrafficShift"
  - AfterAllowTraffic: "LambdaFunctionToValidateAfterTrafficShift"
```

### Deployment Strategies

#### 1. In-Place Deployment (Rolling)

**Aplicação é parada, atualizada e reiniciada** nas mesmas instâncias.

```
Instance 1: v1 → Stop → Update → Start → v2
Instance 2: v1 → Stop → Update → Start → v2
Instance 3: v1 → Stop → Update → Start → v2
```

**Características:**
- 💰 Mais econômico (sem instâncias extras)
- ⏱️ Pode causar downtime
- 🔄 Deploy gradual conforme configuração
- ❌ Não disponível para Lambda

**Quando usar:**
- ✅ Ambientes de desenvolvimento/teste
- ✅ Aplicações que toleram downtime
- ✅ Budget limitado

#### 2. Blue/Green Deployment

**Nova versão é deployada em novo ambiente**, então o tráfego é redirecionado.

```
Blue Environment (v1)  ← Traffic 100%
Green Environment (v2) ← Traffic 0%
           ↓
Blue Environment (v1)  ← Traffic 0%
Green Environment (v2) ← Traffic 100%
```

**Características:**
- ✅ Zero downtime
- ✅ Rollback instantâneo
- ✅ Testes em produção antes de switch
- 💰 Requer recursos duplicados temporariamente

**Tipos de traffic shifting:**

**1. All-at-once**
```
Instant switch: 100% do tráfego de uma vez
```

**2. Canary**
```
10% → Wait → 90%
(Envia pequena % primeiro, depois o resto)
```

**3. Linear**
```
10% → 10 min → 20% → 10 min → 30% → ...
(Incrementa gradualmente)
```

**Quando usar:**
- ✅ Aplicações críticas de produção
- ✅ Quando zero downtime é essencial
- ✅ Precisa testar antes de full rollout

#### Comparison Table

| Aspecto | In-Place | Blue/Green |
|---------|----------|------------|
| **Downtime** | Possível | Zero |
| **Custo** | Menor | Maior |
| **Rollback** | Lento | Instantâneo |
| **Complexidade** | Simples | Moderada |
| **Lambda** | ❌ Não suportado | ✅ Suportado |
| **Testing** | Limitado | Completo antes switch |

### CodeDeploy Agent

**Software** que roda nas instâncias EC2/on-premises para executar deployments.

#### Instalação do Agent

**Amazon Linux 2 / RHEL:**
```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y ruby wget

# Download e instalar
cd /home/ec2-user
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto

# Verificar status
sudo service codedeploy-agent status

# Iniciar automaticamente
sudo systemctl enable codedeploy-agent
```

**Ubuntu:**
```bash
#!/bin/bash
sudo apt update
sudo apt install -y ruby wget

cd /home/ubuntu
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto

sudo service codedeploy-agent status
```

**Windows:**
```powershell
# Download e instalar via PowerShell
New-Item -Path "c:\temp" -ItemType "directory" -Force
Invoke-WebRequest -Uri https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/codedeploy-agent.msi -OutFile c:\temp\codedeploy-agent.msi

Start-Process -FilePath "msiexec.exe" -ArgumentList "/i c:\temp\codedeploy-agent.msi /quiet /l c:\temp\install.log" -Wait

# Verificar serviço
Get-Service -Name codedeployagent
```

### Estrutura de Diretório da Aplicação

```
my-app/
├── appspec.yml
├── scripts/
│   ├── stop_application.sh
│   ├── before_install.sh
│   ├── after_install.sh
│   ├── start_application.sh
│   └── validate_service.sh
├── src/
│   ├── index.html
│   ├── app.js
│   └── styles.css
└── config/
    └── nginx.conf
```

### Exemplos de Scripts de Lifecycle

#### stop_application.sh

```bash
#!/bin/bash
echo "Stopping application..."

# Parar serviço web
sudo systemctl stop nginx

# Ou para aplicação Node.js
# pm2 stop myapp

echo "Application stopped successfully"
```

#### before_install.sh

```bash
#!/bin/bash
echo "Running before install tasks..."

# Criar diretórios necessários
sudo mkdir -p /var/www/html
sudo mkdir -p /var/log/myapp

# Instalar dependências
sudo yum install -y nginx

# Backup da versão anterior
if [ -d "/var/www/html/backup" ]; then
    sudo rm -rf /var/www/html/backup
fi
if [ -d "/var/www/html" ]; then
    sudo mv /var/www/html /var/www/html/backup
fi

echo "Before install completed"
```

#### after_install.sh

```bash
#!/bin/bash
echo "Running after install tasks..."

# Configurar permissões
sudo chown -R nginx:nginx /var/www/html
sudo chmod -R 755 /var/www/html

# Instalar dependências da aplicação (Node.js example)
cd /var/www/html
sudo npm install --production

# Copiar arquivos de configuração
sudo cp /var/www/html/config/nginx.conf /etc/nginx/nginx.conf

echo "After install completed"
```

#### start_application.sh

```bash
#!/bin/bash
echo "Starting application..."

# Iniciar serviço web
sudo systemctl start nginx

# Ou para aplicação Node.js
# cd /var/www/html
# pm2 start app.js

# Habilitar inicialização automática
sudo systemctl enable nginx

echo "Application started successfully"
```

#### validate_service.sh

```bash
#!/bin/bash
echo "Validating service..."

# Aguardar serviço inicializar
sleep 10

# Verificar se serviço está rodando
if sudo systemctl is-active --quiet nginx; then
    echo "Service is running"
else
    echo "Service failed to start"
    exit 1
fi

# Health check HTTP
response=$(curl -s -o /dev/null -w "%{http_code}" http://localhost/)
if [ "$response" -eq "200" ]; then
    echo "Application health check passed"
    exit 0
else
    echo "Application health check failed with status: $response"
    exit 1
fi
```

### Configurar via AWS CLI

#### Criar Application

```bash
aws deploy create-application \
  --application-name MyWebApp \
  --compute-platform Server
```

#### Criar Deployment Group

```bash
aws deploy create-deployment-group \
  --application-name MyWebApp \
  --deployment-group-name Production \
  --deployment-config-name CodeDeployDefault.OneAtATime \
  --ec2-tag-filters Key=Environment,Value=production,Type=KEY_AND_VALUE \
  --service-role-arn arn:aws:iam::123456789012:role/CodeDeployServiceRole \
  --auto-rollback-configuration enabled=true,events=DEPLOYMENT_FAILURE
```

#### Criar Deployment

```bash
# Upload revision para S3
aws s3 cp my-app.zip s3://my-codedeploy-bucket/my-app.zip

# Criar deployment
aws deploy create-deployment \
  --application-name MyWebApp \
  --deployment-group-name Production \
  --s3-location bucket=my-codedeploy-bucket,key=my-app.zip,bundleType=zip \
  --description "Deploy version 1.0.0"
```

#### Monitorar Deployment

```bash
# Listar deployments
aws deploy list-deployments \
  --application-name MyWebApp \
  --deployment-group-name Production

# Obter detalhes do deployment
aws deploy get-deployment \
  --deployment-id d-ABCDEF123

# Acompanhar em tempo real
aws deploy get-deployment \
  --deployment-id d-ABCDEF123 \
  --query 'deploymentInfo.status' \
  --output text
```

#### Stop/Rollback Deployment

```bash
# Parar deployment
aws deploy stop-deployment \
  --deployment-id d-ABCDEF123 \
  --auto-rollback-enabled

# Rollback manual
aws deploy create-deployment \
  --application-name MyWebApp \
  --deployment-group-name Production \
  --revision revisionType=S3,s3Location={bucket=my-codedeploy-bucket,key=previous-version.zip,bundleType=zip}
```

### IAM Roles Necessários

#### CodeDeploy Service Role

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "codedeploy.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**Managed Policy:** `AWSCodeDeployRole`

#### EC2 Instance Profile

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:GetObjectVersion",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-codedeploy-bucket/*",
        "arn:aws:s3:::aws-codedeploy-us-east-1/*"
      ]
    }
  ]
}
```

### Auto Rollback

CodeDeploy pode fazer **rollback automático** em falhas.

**Triggers para rollback:**
- ❌ Deployment fails
- ⚠️ CloudWatch alarm threshold met
- 🚫 Minimum healthy hosts not met

**Configuração:**
```bash
aws deploy update-deployment-group \
  --application-name MyWebApp \
  --current-deployment-group-name Production \
  --auto-rollback-configuration \
    enabled=true,\
    events=DEPLOYMENT_FAILURE,DEPLOYMENT_STOP_ON_ALARM
```

### Integração com CI/CD

#### GitHub Actions

```yaml
name: Deploy to AWS

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Create deployment package
        run: |
          zip -r deploy.zip . -x '*.git*'
      
      - name: Upload to S3
        run: |
          aws s3 cp deploy.zip s3://my-codedeploy-bucket/deploy-${{ github.sha }}.zip
      
      - name: Create CodeDeploy deployment
        run: |
          aws deploy create-deployment \
            --application-name MyWebApp \
            --deployment-group-name Production \
            --s3-location bucket=my-codedeploy-bucket,key=deploy-${{ github.sha }}.zip,bundleType=zip \
            --description "Deployed from commit ${{ github.sha }}"
```

#### AWS CodePipeline

```yaml
# buildspec.yml
version: 0.2

phases:
  install:
    commands:
      - echo Installing dependencies...
      - npm install
  
  build:
    commands:
      - echo Building application...
      - npm run build
  
  post_build:
    commands:
      - echo Creating deployment package...
      - zip -r deploy.zip . -x '*.git*' 'node_modules/*'

artifacts:
  files:
    - '**/*'
  name: MyApp-$(date +%Y%m%d-%H%M%S)
```

**Pipeline Structure:**
```
Source (GitHub) → Build (CodeBuild) → Deploy (CodeDeploy)
```

### Lambda Deployment Example

#### appspec.yml para Lambda

```yaml
version: 0.0
Resources:
  - MyFunction:
      Type: AWS::Lambda::Function
      Properties:
        Name: "MyLambdaFunction"
        Alias: "live"
        CurrentVersion: "1"
        TargetVersion: "2"

Hooks:
  - BeforeAllowTraffic: "BeforeAllowTrafficHook"
  - AfterAllowTraffic: "AfterAllowTrafficHook"
```

#### Deployment Configuration

```bash
# Linear: 10% a cada minuto
aws deploy create-deployment-config \
  --deployment-config-name Linear10PercentEvery1Minute \
  --traffic-routing-config "type=TimeBasedLinear,timeBasedLinear={linearPercentage=10,linearInterval=1}"

# Canary: 10% primeiro, depois 90%
aws deploy create-deployment-config \
  --deployment-config-name Canary10Percent5Minutes \
  --traffic-routing-config "type=TimeBasedCanary,timeBasedCanary={canaryPercentage=10,canaryInterval=5}"
```

### Monitoramento e Debugging

#### CloudWatch Logs

CodeDeploy Agent escreve logs em:

**Linux:**
```
/var/log/aws/codedeploy-agent/codedeploy-agent.log
/opt/codedeploy-agent/deployment-root/deployment-logs/
```

**Windows:**
```
C:\ProgramData\Amazon\CodeDeploy\log\
```

#### Verificar logs via CLI

```bash
# SSH na instância
ssh -i mykey.pem ec2-user@<instance-ip>

# Ver logs do agent
sudo tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log

# Ver logs do deployment específico
sudo tail -f /opt/codedeploy-agent/deployment-root/<deployment-group-id>/<deployment-id>/logs/scripts.log
```

#### Deployment Status

**Possíveis status:**
- 🟡 **Created**: Deployment criado
- 🟡 **Queued**: Na fila
- 🟡 **In Progress**: Em andamento
- 🟢 **Succeeded**: Sucesso
- 🔴 **Failed**: Falhou
- 🔴 **Stopped**: Parado manualmente
- 🟡 **Ready**: Pronto (Blue/Green)

### Troubleshooting Common Issues

#### Problema: Agent não está instalado/rodando

**Solução:**
```bash
# Verificar status
sudo service codedeploy-agent status

# Reiniciar
sudo service codedeploy-agent restart

# Ver logs
sudo tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log
```

#### Problema: Falha no download do bundle

**Causas comuns:**
- ❌ Instance profile sem permissão S3
- ❌ Bucket S3 em região diferente
- ❌ Arquivo não existe no S3

**Solução:**
```bash
# Testar acesso S3 da instância
aws s3 ls s3://my-codedeploy-bucket/

# Verificar IAM role da instância
aws sts get-caller-identity
```

#### Problema: Script hook falhou

**Solução:**
```bash
# Ver logs do script específico
sudo cat /opt/codedeploy-agent/deployment-root/.../logs/scripts.log

# Testar script manualmente
sudo bash /var/www/html/scripts/start_application.sh

# Verificar permissões
ls -la /var/www/html/scripts/
```

#### Problema: Health check falhou

**Verificar:**
```bash
# Aplicação está rodando?
sudo systemctl status nginx

# Porta está escutando?
sudo netstat -tlnp | grep :80

# Firewall bloqueando?
sudo iptables -L

# Health check manual
curl http://localhost/
```

### Boas Práticas CodeDeploy

#### Deployment

- ✅ Use **Blue/Green** para produção
- ✅ Configure **auto-rollback** sempre
- ✅ Implemente **health checks** robustos
- ✅ Use **CloudWatch alarms** para monitorar
- ✅ Teste scripts em ambiente de dev primeiro
- ✅ Mantenha logs detalhados em scripts

#### Scripts

- 📝 Scripts devem ser **idempotentes**
- ⏱️ Configure **timeouts** apropriados
- ✅ Sempre retorne **exit codes corretos** (0 = sucesso, ≠0 = erro)
- 📊 Adicione **logging abundante**
- 🔄 Implemente **retry logic** quando apropriado
- 🧪 **Teste localmente** antes de deployar

#### Segurança

- 🔐 Use **IAM roles** em vez de access keys
- 🔒 Aplique **least privilege** nas policies
- 📦 **Criptografe** bundles no S3
- 🔑 **Rotacione** credenciais regularmente
- 🛡️ Use **VPC endpoints** quando possível

#### Organização

- 🏷️ **Tag** recursos apropriadamente
- 📂 Mantenha **versionamento** de revisions
- 📋 **Documente** processo de deployment
- 🎯 Separe **deployment groups** por ambiente
- 📊 Configure **SNS notifications** para equipe

---

## 🎓 Cenários Práticos Completos

### Cenário 1: Deploy de Aplicação Web Node.js

**Objetivo:** Deploy automatizado de app Node.js com PM2

**Estrutura:**
```
my-node-app/
├── appspec.yml
├── package.json
├── app.js
└── scripts/
    ├── install_dependencies.sh
    ├── start_server.sh
    └── validate.sh
```

**appspec.yml:**
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/myapp
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: ec2-user
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: ec2-user
  ValidateService:
    - location: scripts/validate.sh
      timeout: 300
      runas: ec2-user
```

**install_dependencies.sh:**
```bash
#!/bin/bash
cd /home/ec2-user/myapp
npm install --production
```

**start_server.sh:**
```bash
#!/bin/bash
cd /home/ec2-user/myapp
pm2 stop myapp || true
pm2 start app.js --name myapp
pm2 save
```

**validate.sh:**
```bash
#!/bin/bash
sleep 5
response=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/health)
if [ "$response" -eq "200" ]; then
    exit 0
else
    exit 1
fi
```

### Cenário 2: Blue/Green com Load Balancer

**Setup:**
1. ALB com target groups
2. Auto Scaling Groups (Blue e Green)
3. CodeDeploy Blue/Green deployment

**Fluxo:**
```
1. Deploy para novas instâncias (Green)
2. Registrar no Target Group 2
3. Health checks no Green environment
4. Redirecionar tráfego do ALB
5. Manter Blue por X minutos (rollback rápido)
6. Terminar instâncias Blue
```

### Cenário 3: Lambda com Traffic Shifting

**appspec.yml:**
```yaml
version: 0.0
Resources:
  - MyFunction:
      Type: AWS::Lambda::Function
      Properties:
        Name: "ProcessOrders"
        Alias: "production"
        CurrentVersion: "5"
        TargetVersion: "6"

Hooks:
  - BeforeAllowTraffic: "PreTrafficValidation"
  - AfterAllowTraffic: "PostTrafficValidation"
```

**Deployment Config: Canary**
```
10% traffic → Wait 5 min → 90% traffic
```

---

## 📚 Recursos Adicionais

### Documentação Oficial

- [AWS CLI Documentation](https://docs.aws.amazon.com/cli/)
- [AWS SDK Documentation](https://aws.amazon.com/tools/)
- [Boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
- [AWS SDK for JavaScript](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/)
- [CodeDeploy Documentation](https://docs.aws.amazon.com/codedeploy/)
- [AppSpec File Reference](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html)

### Ferramentas e Utilitários

- **AWS Shell**: Shell interativo para AWS CLI
- **aws-vault**: Gerenciamento seguro de credenciais
- **awslogs**: CLI para CloudWatch Logs
- **LocalStack**: Simula AWS localmente
- **CodeDeploy Agent**: Software nas instâncias

### Certificações Relevantes

- 🔧 **AWS Certified Developer** - Associate
- ☁️ **AWS Certified Solutions Architect**
- 🚀 **AWS Certified DevOps Engineer** - Professional

### Workshops e Hands-On

- [AWS CLI Workshop](https://aws.amazon.com/cli/getting-started/)
- [AWS SDK Workshops](https://workshops.aws/)
- [CodeDeploy Workshop](https://catalog.us-east-1.prod.workshops.aws/workshops/0b8a13b3-2f5c-478e-9ab8-4b8f0bc4f8f0/en-US)

---

## ⚡ Quick Reference

### AWS CLI Cheat Sheet

```bash
# Configurar
aws configure

# Ver configuração
aws configure list

# Ver identidade
aws sts get-caller-identity

# Listar recursos
aws <service> list-* ou describe-*

# Criar recurso
aws <service> create-*

# Deletar recurso
aws <service> delete-*

# Help
aws <service> <command> help
```

### Boto3 Cheat Sheet

```python
import boto3

# Criar client
client = boto3.client('s3')

# Criar resource
s3 = boto3.resource('s3')

# Usar com context manager
with open('file.txt', 'rb') as f:
    client.put_object(Bucket='bucket', Key='key', Body=f)
```

### CodeDeploy Cheat Sheet

```bash
# Criar application
aws deploy create-application --application-name MyApp

# Criar deployment
aws deploy create-deployment \
  --application-name MyApp \
  --deployment-group-name Prod \
  --s3-location bucket=bucket,key=app.zip,bundleType=zip

# Ver status
aws deploy get-deployment --deployment-id <id>

# Stop deployment
aws deploy stop-deployment --deployment-id <id>
```

---

## 🎯 Resumo de Boas Práticas

### AWS CLI

✅ Use profiles para múltiplas contas  
✅ Configure MFA quando possível  
✅ Use --query para filtrar output  
✅ Combine com jq para parsing avançado  
✅ Use --dry-run antes de ações destrutivas  
✅ Crie aliases para comandos frequentes  

### AWS SDKs

✅ Nunca hardcode credenciais  
✅ Use variáveis de ambiente ou IAM roles  
✅ Reutilize clients (thread-safe)  
✅ Implemente retry logic  
✅ Configure timeouts apropriados  
✅ Log requests para debugging  

### CodeDeploy

✅ Use Blue/Green para produção crítica  
✅ Configure auto-rollback sempre  
✅ Implemente health checks robustos  
✅ Scripts devem ser idempotentes  
✅ Teste em dev antes de prod  
✅ Monitore com CloudWatch  

---

<div align="center">

⭐ Se este guia foi útil, considere dar uma estrela!

**Automatize tudo e deploy com confiança! 🚀**

[⬆ Voltar ao topo](#-guia-completo-de-aws-cli-sdks-e-codedeploy)

</div>
