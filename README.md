# Sistema de Serviços Especializados PROAE — SIFU/UFERSA



[Sobre](#-sobre-o-projeto) •
[Funcionalidades](#-funcionalidades) •
[Tecnologias](#-tecnologias) •
[Arquitetura](#-arquitetura) •
[IA Generativa](#-ia-generativa) •
[Como Executar](#-como-executar-o-projeto) •
[Documentação](#-documentação) •
[Autora](#-autora)

> ⚠️ **Projeto em Desenvolvimento:** Este projeto está sendo desenvolvido como trabalho da disciplina de Programação Web — UFERSA 2026.1.

---

## 💡 Sobre o Projeto

Os Serviços Especializados da Pró-Reitoria de Assuntos Estudantis (PROAE) da UFERSA — Psicologia, Serviço Social, Nutrição, Odontologia e Orientação Pedagógica — operam hoje de forma majoritariamente manual e descentralizada. O discente precisa se deslocar fisicamente até a PROAE ou às COAEs dos campi avançados, buscar informações por canais informais (e-mail, WhatsApp, redes sociais) sobre disponibilidade de horários, preencher fichas em papel e aguardar contato posterior do profissional responsável. Não existe canal único digital para agendamento, acompanhamento de histórico de atendimentos ou notificações automáticas.

Este sistema digitaliza e centraliza o fluxo de agendamento, atendimento e acompanhamento dos Serviços Especializados dentro do **SIFU**, com um **assistente de IA** que auxilia o discente na triagem inicial e na identificação do serviço mais adequado à sua demanda antes mesmo do primeiro contato com o profissional.

**Portfólio de referência:** <https://ep.ufersa.edu.br/wp-content/uploads/portfolioep/assistencia/servicosesp/index.html#list>

---

## ✨ Funcionalidades

- **Triagem por IA** — assistente analisa a descrição do discente e sugere o serviço mais adequado (Psicologia, Serviço Social, Nutrição, Odontologia ou Orientação Pedagógica), com justificativa e detecção de urgência
- **Agendamento online** — escolha de data, horário e modalidade (presencial/remota) com confirmação automática
- **Acompanhamento de status** — visualização de todos os agendamentos com status atualizado em tempo real
- **Prontuário eletrônico** — registro de evoluções, encaminhamentos internos e externos e retornos agendados pelo profissional
- **Notificações por e-mail** — confirmação, lembrete 24h antes e cancelamento automático via SES
- **Encaminhamentos** — entre serviços especializados (interno) ou para a rede de saúde do município (externo)
- **Avaliação de atendimento** — formulário pós-consulta com nota de satisfação e comentário
- **Painel do profissional** — agenda diária/semanal, gerenciamento de disponibilidade e relatório mensal de produção
- **Painel gerencial PROAE/COAE** — indicadores consolidados de todos os serviços: taxa de ocupação, perfil dos discentes, tempo médio de espera e avaliações

---

## 🛠 Tecnologias

### Frontend

| Categoria | Tecnologia |
|---|---|
| **Framework** | React 19 + TypeScript |
| **Estilização** | Tailwind CSS |
| **Formulários** | React Hook Form + Zod |
| **Requisições** | Axios |
| **Roteamento** | React Router DOM |
| **Animações** | Motion |
| **IA no browser** | Transformers.js (ONNX Runtime Web) |
| **Ícones** | React Icons |
| **Qualidade** | ESLint + Prettier |

### Backend & Infraestrutura (AWS Serverless)

| Serviço | Uso |
|---|---|
| **AWS Cognito** | Autenticação e sessão de usuários |
| **API Gateway** | Exposição das APIs REST com validação JWT |
| **AWS Lambda (CRUD)** | Lógica de negócio: agendamentos, prontuários, disponibilidade e usuários |
| **AWS Lambda (OBJ)** | Upload e download de arquivos no S3 |
| **DynamoDB** | Banco de dados NoSQL orientado a documentos |
| **S3** | Armazenamento de documentos clínicos e modelo ONNX |
| **Route 53** | Roteamento DNS |
| **SES** | Notificações automáticas por e-mail |

---

## 🏗 Arquitetura

A solução adota arquitetura **serverless** na AWS, com frontend React rodando no navegador e backend composto por funções Lambda acionadas via API Gateway. A triagem por IA ocorre **inteiramente no browser** via Transformers.js, sem custo de servidor adicional.

```
[Navegador / React + Transformers.js]
         |
   [Route 53 / DNS]
         |
   [API Gateway] ←→ [Cognito (Auth)]
         |
   ┌─────┴─────┐
   ▼           ▼
[Lambda CRUD] [Lambda OBJ]
      |              |
[DynamoDB]      [S3 Bucket]
              (prontuários, laudos,
               modelo ONNX)
      |
    [SES]
(notificações por e-mail)
```

> Detalhes completos da arquitetura: [📄 Descrição do Projeto](./docs/descricao_do_projeto.md#2-arquitetura-do-sistema)

**Estimativa de custos AWS:** <https://calculator.aws/#/estimate?id=3556c73ad4b0e05091baaa8011fb6b70b4e511d6>

---

## 🤖 IA Generativa

O assistente de IA é executado **diretamente no browser**, sem nenhuma chamada a servidor externo.

- **Modelo base:** flan-t5-small (Google) com fine-tuning sobre as descrições, critérios de elegibilidade e tipos de demanda dos cinco Serviços Especializados da PROAE/UFERSA
- **Fine-tuning:** realizado no Google Colab com a biblioteca `transformers` da Hugging Face e exportação via `optimum`
- **Formato:** ONNX int8 quantizado (~30–50 MB), hospedado no S3, com CORS habilitado para leitura pelo navegador
- **Execução:** Transformers.js (ONNX Runtime Web) — o modelo é baixado uma vez e cacheado localmente no browser

O assistente analisa a descrição do discente em linguagem natural (ex: *"estou tendo dificuldades de concentração nos estudos e me sentindo ansioso"*) e retorna a sugestão do serviço mais adequado com justificativa. Se identificar palavras ou expressões associadas a situações de crise, sinaliza imediatamente para que o sistema ofereça o contato direto da equipe de Psicologia para atendimento de urgência.

> Detalhes completos: [📄 Descrição do Projeto — seção IA Generativa](./docs/descricao_do_projeto.md#4-ia-generativa-no-projeto)

---

## 🚀 Como Executar o Projeto

### Pré-requisitos

- Node.js 20+
- npm ou pnpm

### 1. Clone o Repositório

```bash
git clone https://github.com/Mkaroline/Web-2026-1-Maria-Karoline.git
cd Web-2026-1-Maria-Karoline
```

### 2. Instale as Dependências

```bash
npm install
```

### 3. Crie o Arquivo de Ambiente

Crie um arquivo `.env` na raiz do projeto baseado no `.env.example`:

```env
VITE_API_URL=https://sua-api-id.execute-api.us-east-1.amazonaws.com
VITE_S3_URL=https://seu-bucket.s3.amazonaws.com
VITE_COGNITO_USER_POOL_ID=us-east-1_XXXXXXXXX
VITE_COGNITO_CLIENT_ID=seu_client_id_aqui
VITE_COGNITO_REGION=us-east-1
```

### 4. Execute em Modo de Desenvolvimento

```bash
npm run dev
```

A aplicação estará disponível em <http://localhost:5173>.

---

## 📁 Estrutura do Projeto

```
Web-2026-1-Maria-Karoline/
├── docs/
│   ├── descricao_do_projeto.md
│   └── diagrams/
│       ├── architecture/
│       │   ├── architecture.puml
│       │   └── architecture.png
│       └── class_diagram/
│           ├── class_diagram.puml
│           └── class_diagram.png
├── src/
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── services/
│   └── main.tsx
├── .env.example
├── package.json
└── README.md
```

---

## 📚 Documentação

| Documento | Descrição |
|---|---|
| [📄 Descrição do Projeto](./docs/descricao_do_projeto.md) | Problema, perfis, dados armazenados, fluxo, telas e IA |
| [💰 Estimativa de Custos AWS](https://calculator.aws/#/estimate?id=3556c73ad4b0e05091baaa8011fb6b70b4e511d6) | Infraestrutura serverless por uso |

---

## 👩‍💻 Autora

**Maria Karoline**

- GitHub: [@Mkaroline](https://github.com/Mkaroline)
- Email: maria.karoline@alunos.ufersa.edu.br
