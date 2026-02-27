# 🎯 Encontros Tech - Plataforma de Gerenciamento de Eventos Tecnológicos

> **Status**: ✅ Em Produção | Aplicação web completa para gestão de eventos de tecnologia com observabilidade integrada

## 📋 Visão Geral

**Objetivo**

Plataforma web para cadastro, visualização e gerenciamento de eventos tecnológicos (workshops, meetups, conferências, hackathons), com sistema de edição baseado em tokens e monitoramento via Prometheus.

**Proposta de Valor**

- **Para Organizadores de Eventos**: Sistema simples para publicar e gerenciar eventos tech sem necessidade de autenticação complexa
- **Para Participantes**: Interface intuitiva para descobrir eventos por tecnologia, localização ou tema
- **Para Operações**: Observabilidade completa com métricas Prometheus e logs estruturados para monitoramento em produção

## ✨ Funcionalidades Implementadas

### 🎪 Gerenciamento de Eventos
- **CRUD Completo** via API REST e interface web
- **Sistema de Edição por Token** - cada evento recebe um UUID único para edições seguras sem login
- **Busca Avançada** - filtros por título, descrição, localização e tecnologias
- **Listagem com Paginação** - suporte a grandes volumes de eventos

### 🔍 Observabilidade
- **Métricas Prometheus** expostas automaticamente via `/metrics`
- **Logs Estruturados** com níveis configuráveis (DEBUG, INFO, WARNING, ERROR)
- **Rastreamento de Requisições** com duração e status code
- **Health Checks** configurados para liveness e readiness probes

### 🎨 Interface Web
- **Páginas HTML** renderizadas com Jinja2
- **Design Responsivo** com CSS customizado
- **Formulários Interativos** para criação e edição de eventos

## 🛠️ Stack Tecnológica

### **Backend**
- **Flask 3.0.0** - Framework web Python minimalista e flexível
- **SQLAlchemy 2.0.43** - ORM para abstração do banco de dados
- **Pydantic 2.11.7** - Validação de schemas e serialização de dados
- **Gunicorn 21.2.0** - WSGI server para produção com 4 workers
- **Prometheus Flask Exporter** - Instrumentação automática de métricas

### **Banco de Dados**
- **PostgreSQL 15** - Banco relacional com suporte a índices e busca textual

### **Frontend**
- **Jinja2 3.1.6** - Template engine para renderização server-side
- **HTML/CSS/JavaScript** - Interface web estática

### **Infraestrutura**
- **Docker & Docker Compose** - Containerização e orquestração local
- **Kubernetes** - Deploy em cluster com alta disponibilidade (3 réplicas)

### **Testes**
- **pytest 8.3.4** - Framework de testes unitários e de integração

## 📁 Estrutura do Projeto

```
encontros-tech/
├── src/
│   ├── core/
│   │   ├── database.py      # Configuração SQLAlchemy e session management
│   │   ├── settings.py      # Variáveis de ambiente e configurações
│   │   └── logging.py       # Sistema de logs estruturados
│   ├── models/
│   │   └── event.py         # Modelo SQLAlchemy da tabela events
│   ├── schemas/
│   │   └── event.py         # Schemas Pydantic para validação
│   ├── services/
│   │   └── event_service.py # Lógica de negócio e operações CRUD
│   ├── routers/
│   │   ├── api_router.py    # Endpoints REST API (/api/events)
│   │   └── page_router.py   # Rotas para páginas HTML
│   ├── templates/           # Templates Jinja2
│   ├── static/              # CSS e JavaScript
│   ├── tests/               # Testes automatizados
│   ├── main.py              # Aplicação Flask principal
│   ├── requirements.txt     # Dependências Python
│   └── Dockerfile           # Imagem Docker da aplicação
├── k8s/
│   └── deploy.yaml          # Manifestos Kubernetes (Deployments + Services)
├── docker-compose.yml       # Orquestração local
├── api-requests.http        # Exemplos de requisições REST Client
└── .env.exemple             # Template de variáveis de ambiente
```

## 🚀 Como Executar

### **Pré-requisitos**
- Docker 20.10+ e Docker Compose 2.0+
- **OU** Python 3.11+ e PostgreSQL 15+

### **Opção 1: Docker Compose (Recomendado)**

```bash
# 1. Clonar o repositório
cd encontros-tech

# 2. Configurar variáveis de ambiente
cp .env.exemple .env

# 3. Subir a aplicação
docker-compose up -d

# 4. Verificar logs
docker-compose logs -f app

# 5. Acessar a aplicação
# Interface Web: http://localhost:8000
# API REST: http://localhost:8000/api/events/
# Métricas: http://localhost:8000/metrics
```

### **Opção 2: Execução Local (Desenvolvimento)**

```bash
# 1. Instalar dependências
cd src
pip install -r requirements.txt

# 2. Configurar PostgreSQL local
# Certifique-se de ter um PostgreSQL rodando em localhost:5432

# 3. Configurar .env
cp .env.example .env
# Edite DATABASE_URL se necessário

# 4. Executar aplicação
python main.py

# Aplicação disponível em http://localhost:8000
```

### **Opção 3: Deploy em Kubernetes**

```bash
# 1. Aplicar manifestos
kubectl apply -f k8s/deploy.yaml

# 2. Verificar status
kubectl get pods -l app=encontros-tech
kubectl get svc encontros-tech-app

# 3. Acessar aplicação
# NodePort: http://<IP-do-node>:30800
# Ou usar port-forward: kubectl port-forward svc/encontros-tech-app 8000:8000
```

## ⚙️ Configuração de Variáveis de Ambiente

| Variável | Descrição | Valor Padrão | Obrigatória |
|----------|-----------|--------------|-------------|
| `DATABASE_URL` | URL de conexão PostgreSQL | `postgresql://encontros_tech:encontros_tech@localhost:5432/encontros_tech` | Sim |
| `POSTGRES_USER` | Usuário do banco | `encontros_tech` | Sim (Docker) |
| `POSTGRES_PASSWORD` | Senha do banco | `encontros_tech` | Sim (Docker) |
| `POSTGRES_DB` | Nome do banco | `encontros_tech` | Sim (Docker) |
| `APP_TITLE` | Título da aplicação | `Encontros Tech` | Não |
| `DEBUG` | Modo debug | `false` | Não |
| `HOST` | Host da aplicação | `0.0.0.0` | Não |
| `PORT` | Porta da aplicação | `8000` | Não |
| `LOG_LEVEL` | Nível de log | `INFO` | Não |
| `LOG_FORMAT` | Formato de log | `colored` | Não |
| `SERVICE_NAME` | Nome do serviço (telemetria) | `encontros-tech` | Não |
| `SERVICE_VERSION` | Versão do serviço | `1.0.0` | Não |

## 📡 API REST - Endpoints Principais

### **Eventos**

```http
# Criar evento
POST /api/events/
Content-Type: application/json

{
  "title": "Workshop Python",
  "description": "Aprenda Python do zero",
  "date": "2024-03-15T19:00:00",
  "location": "São Paulo, SP",
  "technologies": ["Python", "Flask"]
}

# Listar eventos
GET /api/events/
GET /api/events/?search=Python
GET /api/events/?skip=0&limit=10

# Buscar evento por token
GET /api/events/{edit_token}

# Atualizar evento
PUT /api/events/{edit_token}

# Deletar evento
DELETE /api/events/{edit_token}
```

**Resposta de Criação:**
```json
{
  "id": 1,
  "title": "Workshop Python",
  "description": "Aprenda Python do zero",
  "date": "2024-03-15T19:00:00",
  "location": "São Paulo, SP",
  "technologies": ["Python", "Flask"],
  "edit_token": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

> ⚠️ **Importante**: Guarde o `edit_token` retornado na criação - ele é necessário para editar ou deletar o evento!

## 🔍 Modelo de Dados

### **Tabela: events**

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | Integer (PK) | Identificador único |
| `title` | String | Título do evento |
| `description` | Text | Descrição detalhada |
| `date` | DateTime | Data e hora do evento |
| `location` | String | Local do evento |
| `edit_token` | String (Unique) | Token UUID para edição |

> 📝 **Nota**: O campo `technologies` existe apenas nos schemas Pydantic e não é persistido no banco de dados.

## 📊 Monitoramento e Observabilidade

### **Métricas Prometheus**

Acesse `/metrics` para visualizar:
- Requisições HTTP por endpoint e status code
- Duração de requisições (histograma)
- Informações da aplicação (versão, uptime)
- Métricas customizadas de negócio

### **Logs Estruturados**

```python
# Exemplo de log gerado
{
  "timestamp": "2024-02-27T16:00:00Z",
  "level": "INFO",
  "service": "encontros-tech",
  "message": "Evento criado com sucesso",
  "event_id": 1,
  "title": "Workshop Python"
}
```

## 🧪 Testes

```bash
# Executar testes
cd src
pytest

# Com cobertura
pytest --cov=. --cov-report=html

# Testes específicos
pytest tests/services/
```

## 🗺️ Próximos Passos (Roadmap)

### **Fase 1 - Melhorias de Dados**
- [ ] Persistir campo `technologies` no banco (tabela separada com relacionamento N:N)
- [ ] Adicionar campo `organizer` (nome/email do organizador)
- [ ] Implementar campo `capacity` (limite de participantes)

### **Fase 2 - Funcionalidades**
- [ ] Sistema de inscrições em eventos
- [ ] Notificações por email para participantes
- [ ] Exportação de eventos para iCalendar (.ics)
- [ ] Filtros avançados por data e tecnologia

### **Fase 3 - Segurança e Autenticação**
- [ ] Autenticação OAuth2 para organizadores
- [ ] Rate limiting na API
- [ ] Migrar credenciais do banco para Kubernetes Secrets

### **Fase 4 - Infraestrutura**
- [ ] Implementar PersistentVolumeClaim para PostgreSQL
- [ ] Configurar Ingress para acesso externo
- [ ] CI/CD com GitHub Actions
- [ ] Helm Chart para deploy simplificado

### **Fase 5 - Observabilidade Avançada**
- [ ] Integração com Grafana para dashboards
- [ ] Tracing distribuído com OpenTelemetry
- [ ] Alertas automáticos via Alertmanager

## 🤝 Contribuindo

Contribuições são bem-vindas! Para contribuir:

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova funcionalidade'`)
4. Push para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

## 📄 Licença

Este projeto é open source e está disponível sob a licença MIT.

## 📞 Suporte

Para dúvidas ou problemas:
- Abra uma issue no repositório
- Consulte a documentação da API em `/docs` (Swagger UI)
- Verifique os logs da aplicação para troubleshooting

---

**Desenvolvido com ❤️ para a comunidade tech brasileira** 🇧🇷
