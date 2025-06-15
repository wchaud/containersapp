O Azure Container Apps é um serviço gerenciado da Microsoft que permite executar aplicativos em containers de forma simples, escalável e sem a necessidade de gerenciar diretamente servidores, clusters ou orquestradores como o Kubernetes. Ele é ideal para workloads baseadas em microsserviços, APIs, tarefas em background, ou qualquer aplicação containerizada.

Como funciona o Azure Container Apps:
Desenvolvimento:

Você desenvolve seu app normalmente (Node.js, Python, .NET, Java, etc.).

Empacota o app em um container Docker.

Publicação:

Você envia a imagem do container para um registry, como o Azure Container Registry (ACR) ou o Docker Hub.

Deploy:

No Azure, você cria um Container App e aponta para essa imagem.

Define variáveis de ambiente, escalabilidade, recursos (CPU/RAM), e configurações de rede (ex: HTTPS, visibilidade pública ou privada).

Escalabilidade automática:

O serviço usa o KEDA (Kubernetes Event-Driven Autoscaler) para escalar automaticamente com base em eventos: fila cheia, requisições HTTP, mensagens no Event Hub, etc.

Pode escalar para zero, ou seja, não consome recursos quando o app não está em uso (ótimo para economizar).

Ambiente gerenciado:

Você não gerencia o Kubernetes nem precisa configurar balanceadores de carga, certificados, etc.

O ambiente é seguro, isolado e pode ser integrado a outros serviços do Azure.

Exemplo: Página HTML simples em Azure Container App
Cenário:
Você tem um site simples com index.html e quer hospedá-lo no Azure usando Container Apps.

1. Estrutura dos arquivos

site-html/
│
├── Dockerfile
├── index.html
└── styles.css
index.html:

<!DOCTYPE html>
<html>
<head>
  <title>Meu Site no Azure</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Olá, mundo!</h1>
  <p>Este site está rodando no Azure Container Apps.</p>
</body>
</html>
styles.css:

body {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  color: #333;
  text-align: center;
  margin-top: 50px;
}
2. Dockerfile
Vamos usar o nginx como servidor para hospedar os arquivos estáticos:

FROM nginx:alpine
COPY . /usr/share/nginx/html
Esse Dockerfile:

Usa a imagem leve do Nginx.

Copia seus arquivos HTML/CSS para a pasta padrão do Nginx (/usr/share/nginx/html).

3. Construir e publicar a imagem
Se você usa o Azure Container Registry (ACR):

# Login no Azure e ACR
az login
az acr login --name <nome-do-registro>

# Build da imagem
docker build -t <nome-do-registro>.azurecr.io/meu-site-html:v1 .

# Push da imagem
docker push <nome-do-registro>.azurecr.io/meu-site-html:v1
🚀 4. Criar o Azure Container App
Você pode usar o portal, mas aqui vai via CLI:

az containerapp env create \
  --name meu-ambiente \
  --resource-group meu-grupo \
  --location eastus

az containerapp create \
  --name meu-site-html \
  --resource-group meu-grupo \
  --environment meu-ambiente \
  --image <nome-do-registro>.azurecr.io/meu-site-html:v1 \
  --target-port 80 \
  --ingress external \
  --registry-server <nome-do-registro>.azurecr.io \
  --cpu 0.5 --memory 1.0Gi
  
5. Resultado final
Você receberá uma URL como:

https://meu-site-html.eastus.azurecontainerapps.io
Acessando essa URL, verá seu site HTML rodando!

