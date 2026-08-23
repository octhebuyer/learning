# Guia Completo de Automação & CI/CD no GitHub Actions (Nível Expert)

A automação do ciclo de vida de desenvolvimento de software (*Software Development Life Cycle - SDLC*) é uma das competências fundamentais de um engenheiro de software sénior ou especialista em DevOps. O **GitHub Actions** permite orquestrar pipelines de *Continuous Integration* (CI) e *Continuous Deployment* (CD) integradas diretamente no repositório do código.

---

## 1. Fundamentos e Arquitetura do GitHub Actions

Para compreender o funcionamento da plataforma, é essencial dominar os conceitos base da sua arquitetura:

* **Workflow:** Processo automatizado configurado em YAML que reside no diretório `.github/workflows/`. Um repositório pode ter múltiplos workflows com objetivos distintos (ex: testes em PRs, releases automáticas, deploy em produção).
* **Events (Triggers):** Atividades que iniciam a execução de um workflow (ex: `push`, `pull_request`, `schedule`, `workflow_dispatch`).
* **Jobs:** Conjunto de passos (*steps*) executados no mesmo *runner*. Por omissão, múltiplos *jobs* executam em paralelo.
* **Steps:** Tarefas individuais executadas sequencialmente dentro de um *job*. Podem ser comandos shell ou chamadas a *actions*.
* **Actions:** Blocos de código reutilizáveis que realizam tarefas complexas (ex: configurar um ambiente Python/Node.js, autenticar numa cloud).
* **Runners:** Servidores que executam os *jobs* quando um workflow é ativado. Podem ser geridos pelo GitHub (*GitHub-hosted*) ou pela organização (*Self-hosted*).

---

## 2. Padrões Avançados de Orquestração

### 2.1 Gestão de Dependências entre Jobs (`needs`)
Por omissão, os *jobs* correm em paralelo. A diretiva `needs` cria um Grafo Aclíclico Dirigido (DAG) para forçar uma ordem de execução.

```yaml
name: Pipeline CI/CD

on:
  push:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test

  build:
    needs: [lint, test] # Só executa se lint e test passarem com sucesso
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run build

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying to production..."
```

---

### 2.2 Estratégia de Matriz (`matrix strategy`)
Multiplica a execução de um *job* em múltiplos ambientes e versões de linguagens, otimizando o tempo total de validação.

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false # Não interrompe os restantes se um falhar
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        python-version: ['3.10', '3.11', '3.12']
        exclude:
          - os: windows-latest
            python-version: '3.10'
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - run: python -m pytest
```

---

### 2.3 Workflows Reutilizáveis (`reusable workflows`)
Permitem centralizar e padronizar pipelines em toda a organização, evitando a duplicação de ficheiros YAML.

#### Workflow Reutilizável (`.github/workflows/reusable-build.yml`):
```yaml
name: Template de Build

on:
  workflow_call:
    inputs:
      node-version:
        required: true
        type: string
    secrets:
      NPM_TOKEN:
        required: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
      - run: npm ci
      - run: npm run build
```

#### Workflow Principal (`.github/workflows/app-ci.yml`):
```yaml
jobs:
  call-reusable-build:
    uses: ./.github/workflows/reusable-build.yml
    with:
      node-version: '20.x'
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## 3. Custom Actions (Criar Ações Próprias)

Quando as *actions* públicas do Marketplace não satisfazem necessidades organizacionais, criam-se *actions* personalizadas.

| Tipo | Descrição | Casos de Uso Ideal |
| :--- | :--- | :--- |
| **Composite Actions** | Agrupa múltiplos passos de shell ou outras actions num só ficheiro. | Reutilização de sequências repetitivas de comandos. |
| **JavaScript Actions** | Corre nativamente no runner via Node.js runtime. | Operações rápidas, cross-platform e integração leve com a API do GitHub. |
| **Docker Container Actions** | Encapsula dependências e código dentro de uma imagem Docker. | Lógicas complexas que exigem ferramentas específicas de sistema operativo (apenas Linux). |

---

## 4. Infraestrutura de Runners (GitHub-Hosted vs Self-Hosted)

### 4.1 GitHub-Hosted Runners
* Geridos totalmente pelo GitHub em máquinas virtuais efémeras e isoladas.
* Ideais para a grande maioria dos fluxos de trabalho de software público e empresarial padrão.

### 4.2 Self-Hosted Runners
* Servidores ou instâncias virtuais mantidos pela própria empresa.
* **Casos de Uso:**
  * Acesso a redes privadas e intranets protegidas por firewall.
  * Requisitos de hardware específicos (GPUs, Apple Silicon, arquiteturas ARM).
  * Otimização de custos para volumes massivos de minutos de CI.
* **Recomendação de Segurança:** **Nunca utilize Self-Hosted Runners em repositórios públicos.** *Pull Requests* de utilizadores externos podem executar código malicioso no seu ambiente de rede interno.
* **Autoscaling:** Utilização do **Actions Runner Controller (ARC)** em Kubernetes para dimensionamento dinâmico de *runners* efémeros.

---

## 5. Governança, Segredos e Ambientes de Deploy

### 5.1 GitHub Environments & Protection Rules
Permitem definir regras de segurança estritas antes que as alterações sejam publicadas em ambientes críticos (ex: `staging`, `production`).

* **Required Reviewers:** Exige aprovação manual de pessoas ou equipas qualificadas antes do arranque do *job*.
* **Wait Timers:** Adiciona uma pausa temporizada obrigatória antes da execução.
* **Deployment Branches:** Restringe os *deploys* a *branches* ou *tags* autorizados (ex: apenas `main`).

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://meusite.com
    steps:
      - run: ./deploy.sh
```

---

### 5.2 Estratégias Seguras de Segredos & Autenticação Cloud
* **Hierarquia de Escopo:** O GitHub suporta segredos ao nível de **Organização**, **Repositório** e **Ambiente**. O escopo de Ambiente tem prioridade sobre o de Repositório.
* **Mascaramento Automático:** Valores definidos em `secrets` são automaticamente mascarados (`***`) nas consolas de log.
* **Autenticação sem Palavras-passe via OIDC (OpenID Connect):**
  A abordagem moderna de segurança consiste em **não armazenar chaves estáticas de longa duração** (ex: `AWS_SECRET_ACCESS_KEY`) no GitHub. Em vez disso, utiliza-se OIDC para estabelecer uma relação de confiança federada entre o GitHub Actions e os fornecedores cloud (AWS, Azure, GCP), emitindo *tokens* temporários de curta duração.

```yaml
permissions:
  id-token: write # Necessário para solicitar o token JWT do OIDC
  contents: read

steps:
  - name: Autenticar na AWS via OIDC
    uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsWorkflowRole
      aws-region: eu-west-1
```

---

## 6. Recursos e Links para Documentação Oficial

* [Documentação Oficial do GitHub Actions](https://docs.github.com/actions)
* [Sintaxe do Ficheiro de Workflow](https://docs.github.com/actions/writing-workflows/workflow-syntax-for-github-actions)
* [Guia de Utilização de Environments](https://docs.github.com/actions/managing-workflow-runs-and-deployments/managing-deployments/using-environments-for-deployment)
* [Segurança de Deployments com OIDC](https://docs.github.com/actions/security-for-github-actions/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
