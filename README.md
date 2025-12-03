# Simple Resource API

API REST para catálogo de produtos desenvolvida com Django + Django REST Framework.

---

## Seção 1: Instruções para Rodar

### Variáveis de Ambiente

Crie um arquivo `.env` na raiz do projeto baseado no `.env.example`:

```env
# Django
DJANGO_SECRET_KEY="sua_chave_secreta_aqui"
DJANGO_DEBUG="1"
DJANGO_ALLOWED_HOSTS="127.0.0.1,localhost"

# Banco de Dados PostgreSQL
DATABASE_ENGINE="postgresql"
DATABASE_USERNAME="db_user"
DATABASE_PASSWORD="db_password"
DATABASE_NAME="simple_resource_db"
DATABASE_HOST="localhost"
DATABASE_PORT="5432"

# AWS S3 ou Cloudflare R2 (para upload de imagens)
AWS_ACCESS_KEY_ID="sua_access_key"
AWS_SECRET_ACCESS_KEY="sua_secret_key"
AWS_STORAGE_BUCKET_NAME="nome_do_bucket"
AWS_S3_REGION_NAME="auto"
AWS_S3_ENDPOINT_URL="https://seu-account-id.r2.cloudflarestorage.com"
```

#### Explicação das Variáveis

| Variável                  | Descrição                                                                                                               |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `DJANGO_SECRET_KEY`       | Chave secreta usada pelo Django para criptografia (sessões, tokens CSRF). Em produção, use uma chave longa e aleatória. |
| `DJANGO_DEBUG`            | Modo debug. Use `1` para desenvolvimento (mostra erros detalhados) e `0` para produção.                                 |
| `DJANGO_ALLOWED_HOSTS`    | Lista de domínios/IPs que podem acessar a API. Separe múltiplos valores com vírgula.                                    |
| `DATABASE_ENGINE`         | Tipo de banco de dados. Use `postgresql` para PostgreSQL ou `sqlite3` para SQLite.                                      |
| `DATABASE_USERNAME`       | Usuário do banco de dados PostgreSQL.                                                                                   |
| `DATABASE_PASSWORD`       | Senha do usuário do banco de dados.                                                                                     |
| `DATABASE_NAME`           | Nome do banco de dados a ser utilizado.                                                                                 |
| `DATABASE_HOST`           | Host do banco. Use `localhost` localmente ou `db` no Docker Compose.                                                    |
| `DATABASE_PORT`           | Porta do PostgreSQL (padrão: `5432`).                                                                                   |
| `AWS_ACCESS_KEY_ID`       | Chave de acesso da API do S3/R2. Obtida no dashboard da AWS ou Cloudflare.                                              |
| `AWS_SECRET_ACCESS_KEY`   | Chave secreta da API do S3/R2. Mantenha em segredo!                                                                     |
| `AWS_STORAGE_BUCKET_NAME` | Nome do bucket onde as imagens serão armazenadas.                                                                       |
| `AWS_S3_REGION_NAME`      | Região do bucket. Use `auto` para R2 ou a região AWS (ex: `us-east-1`).                                                 |
| `AWS_S3_ENDPOINT_URL`     | URL do endpoint S3. Para R2: `https://<account-id>.r2.cloudflarestorage.com`. Deixe vazio para AWS S3.                  |

---

### Configuração do Cloudflare R2 (ou AWS S3)

#### Para Cloudflare R2:

1. Acesse o [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. Vá em **R2 Object Storage** no menu lateral
3. Clique em **Create bucket** e dê um nome (ex: `django`)
4. Após criar, vá em **Manage R2 API Tokens**
5. Clique em **Create API Token**
6. Selecione as permissões:
   - **Object Read & Write** para o bucket criado
7. Copie as credenciais geradas:
   - **Access Key ID** → `AWS_ACCESS_KEY_ID`
   - **Secret Access Key** → `AWS_SECRET_ACCESS_KEY`
8. O endpoint será: `https://<ACCOUNT_ID>.r2.cloudflarestorage.com`
   - O Account ID está no canto superior direito do dashboard R2

#### Para AWS S3:

1. Acesse o [AWS Console](https://console.aws.amazon.com/)
2. Vá em **S3** e crie um bucket
3. Vá em **IAM** → **Users** → crie um usuário com acesso programático
4. Anexe a policy `AmazonS3FullAccess` ou uma policy customizada
5. Copie as credenciais para o `.env`
6. Deixe `AWS_S3_ENDPOINT_URL` vazio (ou remova a variável)

---

### Instalação Local (sem Docker)

```bash
# Clone o repositório
git clone https://github.com/wandrey7/simple-resource-api
cd simple-resource-api

# Crie e ative o ambiente virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou: venv\Scripts\activate  # Windows

# Instale as dependências
pip install -r requirements.txt

# Entre na pasta do projeto Django
cd store_api

# Execute as migrações do banco
python manage.py migrate

# Crie um superusuário para acessar o admin
python manage.py createsuperuser
# Siga as instruções (email, usuário, senha)

# Inicie o servidor
python manage.py runserver
```

---

### Instalação com Docker

```bash
# Suba os containers (PostgreSQL + Django)
docker-compose up --build

# Em outro terminal, execute as migrações
docker-compose exec django-web python manage.py migrate

# Crie o superusuário
docker-compose exec django-web python manage.py createsuperuser
```

---

### URLs Disponíveis

| Recurso               | URL                                   |
| --------------------- | ------------------------------------- |
| **API de Produtos**   | http://127.0.0.1:8000/api/products/   |
| **API de Categorias** | http://127.0.0.1:8000/api/categories/ |
| **Django Admin**      | http://127.0.0.1:8000/admin/          |
| **Swagger (Docs)**    | http://127.0.0.1:8000/swagger/        |
| **ReDoc (Docs)**      | http://127.0.0.1:8000/redoc/          |

---

## Seção 2: Decisões de Design

### Maior Dificuldade Encontrada

**Problema:** O upload de imagens para o Cloudflare R2 não funcionava. A imagem era salva no banco como string literal (`products/caminho-da-imagem.png`) em vez de ser enviada para o R2.

**Causa raiz:**

1. Django 5.2 descontinuou `DEFAULT_FILE_STORAGE` — agora é necessário usar o dicionário `STORAGES`
2. O `load_dotenv()` estava sendo chamado antes de definir `BASE_DIR`, então o arquivo `.env` não era encontrado

**Solução:**

```python
# settings.py - Sintaxe correta para Django 4.2+
STORAGES = {
    "default": {
        "BACKEND": "storages.backends.s3boto3.S3Boto3Storage",
    },
    "staticfiles": {
        "BACKEND": "django.contrib.staticfiles.storage.StaticFilesStorage",
    },
}

# Carregar .env do local correto
BASE_DIR = Path(__file__).resolve().parent.parent
load_dotenv(BASE_DIR.parent / '.env')
```

---

## Rodando os Testes

```bash
cd store_api
python manage.py test api
```

Ou com Docker:

```bash
docker-compose exec django-web python manage.py test api
```
