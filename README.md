Guia Definitivo: Instalação e Configuração do n8n Local com Docker
O n8n é uma poderosa plataforma de automação de fluxo de trabalho fair-code, baseada em nós (nodes), que permite conectar qualquer API e manipular dados livremente. Instalar o n8n localmente usando Docker é a maneira mais recomendada, pois garante isolamento, facilidade de atualização e portabilidade entre diferentes sistemas.
Este roteiro técnico guiará você desde os requisitos de sistema até a execução e o primeiro acesso.

1. Requisitos de Sistema
Antes de iniciar, certifique-se de que sua máquina atende aos requisitos básicos para rodar contêineres Docker.

Windows
Sistema Operacional: Windows 10 (versão 2004+) ou Windows 11.
Recurso Necessário: WSL 2 (Windows Subsystem for Linux) ativado.
Software: Docker Desktop para Windows. Certifique-se de que a opção de usar a engine baseada em WSL 2 esteja marcada nas configurações do Docker Desktop.

macOS
Sistema Operacional: macOS Sierra 10.12 ou superior.
Hardware: Suporte tanto para processadores Intel quanto para Apple Silicon (Série M).
Software: Docker Desktop para Mac.

Ubuntu / Linux
Sistema Operacional: Ubuntu 20.04 LTS, 22.04 LTS ou 24.04 LTS (ou distribuições equivalentes).
Software: * Docker Engine instalado (via repositório oficial apt do Docker).
Docker Compose Plugin instalado (sudo apt-get install docker-compose-plugin).
Dica Geral: O n8n precisa de pelo menos 1GB de RAM dedicada para rodar fluxos básicos, mas recomenda-se 2GB ou mais dependendo da complexidade das suas automações.

2. Métodos de Instalação
Existem duas formas principais de iniciar o n8n com Docker:
Docker CLI (Comando Direto): Ótimo para testes rápidos.
Docker Compose: Recomendado para ambientes persistentes e duradouros, pois facilita a configuração de variáveis de ambiente e volumes.
Neste guia, utilizaremos o Docker Compose, pois é a melhor prática para garantir que seus fluxos, credenciais e configurações não sejam perdidos quando o contêiner for reiniciado.

3. Passo a Passo de Instalação (Docker Compose)

Passo 3.1: Criar o diretório do projeto
Abra o seu terminal (PowerShell no Windows, Terminal no macOS/Linux) e crie uma pasta dedicada para os arquivos do n8n.
mkdir n8n-local
cd n8n-local

Passo 3.2: Criar o arquivo de configuração
Dentro da pasta n8n-local, crie um arquivo chamado docker-compose.yml. Você pode usar qualquer editor de texto (como VS Code, Nano, ou Bloco de Notas).
Cole o seguinte conteúdo no arquivo:
```yaml
version: '3.8'

volumes:
  n8n_data:

services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    container_name: n8n_local
    restart: always
    ports:
      - "5678:5678"
    environment:
      # Configurações de Fuso Horário (Altere para o seu fuso, se necessário)
      - GENERIC_TIMEZONE=America/Sao_Paulo
      - TZ=America/Sao_Paulo
      
      # Configurações de Ambiente
      - NODE_ENV=production
      
      # URLs para funcionamento interno e Webhooks
      - WEBHOOK_URL=http://localhost:5678/
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
    volumes:
      - n8n_data:/home/node/.n8n
```

O que este arquivo faz?
image: Baixa a versão oficial e mais recente do n8n.
restart: always: Garante que o n8n reinicie automaticamente se o Docker reiniciar (ou se o PC for reiniciado).
ports: Mapeia a porta 5678 do seu computador para o contêiner.
environment: Define variáveis cruciais. O GENERIC_TIMEZONE é importante para que agendamentos (Cron jobs) funcionem no horário correto.
volumes: Cria um volume persistente chamado n8n_data. Isso é essencial para não perder seus fluxos ao parar o Docker.

Passo 3.3: Iniciar o contêiner
Ainda no terminal, dentro da pasta onde o docker-compose.yml está salvo, execute o comando:
```bash
docker compose up -d
```

(Se o seu Docker for muito antigo, pode ser necessário usar docker-compose up -d com hífen).
O Docker baixará a imagem do n8n (pode levar alguns minutos na primeira vez) e iniciará o serviço em segundo plano (graças à flag -d).
Você pode verificar se está rodando corretamente com:
```bash
docker ps
```

4. Primeiro Uso e Configuração Inicial

4.1: Acessar a interface
Abra o seu navegador web (Chrome, Edge, Firefox, etc.) e digite o seguinte endereço:
http://localhost:5678

4.2: Criar a Conta de Proprietário (Owner Account)
Ao abrir a página pela primeira vez, o n8n solicitará que você crie uma conta.
Preencha seus dados: Email, Nome de Usuário e Senha.
Nota: Como a instalação é local, este login e senha ficam armazenados apenas no seu computador (dentro do volume Docker), não vão para a nuvem.
Responda ao breve questionário de uso (opcional).

4.3: Tela Principal
Após o login, você verá o "Canvas" do n8n. Você agora pode:
Clicar em "Add first step" para adicionar o seu primeiro Nó de Gatilho (Trigger Node).
Navegar na aba Templates no menu lateral esquerdo para importar fluxos de automação prontos da comunidade.

5. Dicas Adicionais e Manutenção

Como atualizar o n8n?
O n8n lança atualizações com frequência (novos nós e correções). Para atualizar sua instância local, vá até a pasta n8n-local no terminal e execute:
```bash
# 1. Baixa a versão mais recente da imagem
docker compose pull

# 2. Recria o contêiner com a nova imagem (mantendo seus dados seguros no volume)
docker compose up -d

# 3. (Opcional) Remove imagens antigas que não estão sendo usadas para liberar espaço
docker image prune -f
```

Como parar ou remover o n8n?
Para pausar (parar) o servidor: `docker compose stop`
Para reiniciar (voltar a rodar): `docker compose start`
Para destruir o contêiner (mas manter os dados salvos no volume): `docker compose down`

Se precisar ver os logs (para diagnosticar erros de alguma API ou fluxo):
```bash
docker compose logs -f
```

Pronto! Seu ambiente n8n está completamente funcional e configurado para desenvolvimento local de automações de forma segura e persistente.
