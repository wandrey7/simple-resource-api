* IMPORTANTE: Não inicie este desafio sem autorização. O desafio só poderá ser iniciado no dia e horário agendado via Google Meet. Entre em contato via email ou whatsapp:
  - administrativo@infinixassessoria.com.br
  - (21) 99515-2411

# SIMPLE RESOURCE API

## Sobre
**Stack**: Python + Django + DRF
**Escopo**: Uma API para catálogos de produtos.

## Requisitos Essenciais (Timebox 4h)

1. Modelos: Em api/models.py, defina:
    - Category(name: CharField)
    - Product(name: Charfield, description: TextField, price: DecimalField (2 decimal places), category: ForeignKey(Category, on_delete=models.PROTECT))
2. Admin: Registre ambos os modelos *Category* e *Product* no api/admin.py para que sejam gerenciáveis via Django Admin.
3. API (DRF):
    - Defina *serializers* para *Category* e *Product*.
    - O serializador de *Product* deve exibir o nome da categoria, não apenas seu ID.
    - Use **ViewSets** para fornercer funcionalidade CRUD completa para *Category* e *Product*.
    - Configure urls usando DefaultRouter do DRF para registrar os ViewSets.
4. Testes: Escreva pelo menos 2 (dois) testes unitários usando APITestCase do DRF:
    - Um teste para verificar a criação de um Produto.
    - Um teste para verificar a listagem de Produtos.

#### Bônus (Desejáveis):
    - Swagger (Documentação API: Configure e adicione as rotas ao urls.py principal.
    - AWS S3 (Upload de Arquivos): Adicione um campo ImageField(upload_to='products/') ao modelo Product; Configue django-storages e boto3 e dê instruções sobre como configurar as credenciais da AWS para testar o upload.
    - Hospedagem: Forneça um Dockerfile e docker-compose.yml que permitam rodar o projeto e seu banco de dados (PostgersSQL) com um único comando. Se preferir, faça deploy no Render.

## Instruções sobre "README-CANDIDATO" (Timebox 30min):
Preencha este arquivo com informações claras e concisas, separadas pelas seguintes seções:

#### Seção 1: Instruções para rodar
- Quais variáveis de ambiente são necessárias?
- Como instalar dependências?
- Como rodar o projeto?

#### Seção 2: Decisões de design
- Qual foi a maior dificuldade que você encontrou e como superou?
- O que você não teve tempo de fazer (dentro do timebox) e como você faria se tivesse mais tempo?

#### Seção 3: Link para Deploy (Bônus)
- Cole aqui o link do projeto hospedado ou instrua como rodar via Docker.

#### Seção final: Recomendações
- Escreva aqui dicas, melhorias e recomendações sobre este desafio.

## Considerações finais:
Este desafio não foi pensado para encontrar quem o finaliza 100% ou quem o termina mais rápido. Estamos buscando um desenvolvedor sério, que saiba como desenvolver soluções mesmo que para apenas 50% do projeto. Não queremos nenhum dev que dependa 100% de IA ou de terceiros, mas sim aquele que sabe priorizar, desenvolver e pesquisar.
