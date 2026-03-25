# Descrição do projeto de Serviços Especializados da PROAE a ser desenvolvido para o Sistema Integrado Funcional e Unificado (SIFU)

---

## 1. Descrição do problema a ser solucionado

**Link do portfólio de referência:** https://ep.ufersa.edu.br/wp-content/uploads/portfolioep/assistencia/servicosesp/index.html#list

Os Serviços Especializados da Pró-Reitoria de Assuntos Estudantis (PROAE) da UFERSA — Psicologia, Serviço Social, Nutrição, Odontologia e Orientação Pedagógica — operam hoje de forma majoritariamente manual e descentralizada. O discente que necessita de atendimento precisa deslocar-se fisicamente até a PROAE ou às COAEs dos campi avançados, buscar informações por canais informais (e-mail, WhatsApp, redes sociais) sobre disponibilidade de horários, preencher fichas em papel e aguardar contato posterior do profissional responsável. Não existe um canal único digital para agendamento, acompanhamento de histórico de atendimentos ou notificações automáticas sobre retornos agendados.

Além disso, os profissionais dos serviços não dispõem de uma ferramenta centralizada para gerenciar suas agendas, registrar prontuários de acompanhamento e gerar relatórios de produção para a gestão da PROAE. A ausência de dados consolidados dificulta a tomada de decisão sobre alocação de recursos e identificação de perfis de vulnerabilidade estudantil.

O sistema proposto digitaliza e centraliza o fluxo de agendamento, atendimento e acompanhamento dos Serviços Especializados dentro do SIFU, com um assistente de IA que auxilia o discente na triagem inicial e na identificação do serviço mais adequado à sua demanda antes mesmo do primeiro contato com o profissional.

---

## 1.1 Perfis de usuário

**Discente**
Solicita agendamento em qualquer um dos Serviços Especializados disponíveis. Preenche o formulário de triagem inicial com a descrição de sua demanda. Usa o assistente de IA para ser orientado sobre qual serviço procurar. Recebe notificações automáticas de confirmação, lembrete e cancelamento de consultas. Visualiza seu histórico de atendimentos e encaminhamentos realizados. Pode reagendar ou cancelar consultas pelo próprio sistema. Avalia o atendimento recebido ao final de cada sessão.

**Técnico-Administrativo (Profissional do Serviço Especializado)**
Gerencia sua agenda de atendimentos: visualiza os agendamentos do dia/semana, confirma ou recusa solicitações, define seus horários disponíveis e o número máximo de atendimentos por período. Registra prontuários e evoluções de cada sessão. Realiza encaminhamentos internos (de um serviço a outro) ou externos (rede de saúde do município). Gera relatórios de produção mensais. Acessa o histórico completo de atendimentos do discente encaminhado.

**Gestão da PROAE (Técnico-Administrativo)**
Acessa o painel gerencial com indicadores consolidados de todos os serviços: total de atendimentos por serviço, taxa de ocupação das agendas, perfil dos discentes atendidos (curso, campus, faixa de renda, tipo de demanda) e tempo médio de espera. Gerencia os profissionais vinculados a cada serviço. Publica comunicados e informações sobre os serviços. Exporta relatórios para prestação de contas institucional.

**Coordenadoria de Assuntos Estudantis dos Campi (COAE)**
Acessa os agendamentos e atendimentos realizados em seu campus específico. Visualiza os indicadores locais e identifica demandas recorrentes. Realiza encaminhamentos de discentes para a PROAE central quando necessário.

---

## 1.2 Informações a serem armazenadas

### Agendamento

- Tipo de serviço solicitado: `PSICOLOGIA` | `SERVICO_SOCIAL` | `NUTRICAO` | `ODONTOLOGIA` | `ORIENTACAO_PEDAGOGICA`
- Status: `PENDENTE` | `CONFIRMADO` | `CANCELADO_DISCENTE` | `CANCELADO_PROFISSIONAL` | `REALIZADO` | `FALTOU`
- Data e horário da consulta
- Campus de atendimento
- Motivo/demanda descrita pelo discente (texto livre)
- Resultado da triagem por IA (serviço sugerido e justificativa)
- Indicador de urgência (identificado pelo assistente de IA ou pelo profissional)
- Data e hora do agendamento
- Modalidade: presencial ou remota

### Prontuário / Evolução

- Identificador único do atendimento
- Profissional responsável
- Data, hora e duração da sessão
- Registro de evolução (texto clínico, visível apenas pelo profissional e gestão)
- Encaminhamento realizado: interno (serviço destino) ou externo (descrição do serviço externo e unidade)
- Retorno agendado (data prevista)
- Anexos (laudos, declarações) — armazenados no S3

### Disponibilidade de Agenda

- Profissional
- Dia da semana e horários disponíveis
- Capacidade máxima de atendimentos por turno
- Períodos de bloqueio (férias, feriados, eventos)
- Campus e modalidade (presencial/remoto)

### Avaliação de Atendimento

- Nota de satisfação (1 a 5)
- Comentário textual (opcional)
- Data de envio

### Usuários

- **Discente:** matrícula, CPF, nome, curso, campus, e-mail, telefone, indicador de beneficiário da assistência estudantil
- **Profissional:** SIAPE, CPF, nome, serviço especializado de vinculação, campus, e-mail, CRP/CRN/CRO (conforme categoria)
- **Gestão PROAE / COAE:** SIAPE, nome, cargo, campus, e-mail

---

## 1.3 Estimativa de preço da infraestrutura AWS

A infraestrutura utiliza serviços serverless, com cobrança por uso — sem custo fixo de servidores ociosos. Os valores abaixo são estimados com base no uso esperado do sistema para atendimento dos campi da UFERSA (Mossoró, Pau dos Ferros, Caraúbas e Angicos).

| Serviço | Configuração utilizada | Custo mensal |
|---|---|---|
| Amazon S3 | 30 GB armazenamento, 50 GB egress/mês, 30.000 requisições GET | $5,80 |
| Amazon Route 53 | 1 zona hospedada | $0,90 |
| Amazon SES | 3.000 e-mails enviados/mês | $0,30 |
| Amazon DynamoDB | Tabela Standard, itens de 1 KB, 1 GB de dados | $0,27 |
| Amazon API Gateway | 30.000 requisições/mês, tamanho médio 10 KB | $0,10 |
| AWS Lambda — CRUD | 30.000 invocações/mês, 512 MB | $0,00 |
| AWS Lambda — OBJ | 5.000 invocações/mês, 512 MB | $0,00 |
| Amazon Cognito | 1.500 MAUs ativos/mês | $0,00 |
| **Total mensal** | | **$7,37** |
| **Total anual (12 meses)** | | **$88,44** |

> O S3 representa 79% do custo mensal, principalmente pelo armazenamento e download de prontuários e do modelo ONNX. Após cache no browser, o egress tende a reduzir nos meses seguintes.

**URL pública da estimativa:** https://calculator.aws/#/estimate?id=sifu-proae-servicos-especializados *(a ser gerada após cadastro na calculadora AWS)*

---

## 2. Arquitetura do sistema

A solução adota arquitetura **serverless** na AWS, com frontend React rodando no navegador do usuário e backend composto por funções Lambda acionadas via API Gateway. O assistente de triagem por IA executa inteiramente no navegador — sem Lambda dedicada — usando o Transformers.js com modelo ONNX quantizado hospedado no S3.

### Componentes

**Client (Navegador)**
Aplicação React + TypeScript estilizada com Tailwind CSS. O Transformers.js carrega o modelo ONNX do S3 uma única vez e o cacheia localmente no navegador. Bibliotecas: React, TypeScript, Tailwind CSS, Axios, Zod, React Hook Form, React Router DOM, Motion, React Icons, Transformers.js.

**Route 53** — resolve o DNS da aplicação.

**API Gateway** — ponto de entrada de todas as requisições. Valida o token do Cognito antes de encaminhar para as Lambdas.

**Cognito** — gerencia autenticação e sessão de todos os perfis de usuário.

**Lambda CRUD** — processa operações de agendamentos, prontuários, disponibilidade e usuários. Lê e escreve no DynamoDB. Dispara notificações via SES em mudanças de status de agendamento.

**Lambda OBJ** — gerencia upload e download de arquivos (laudos, declarações, anexos de prontuário) no S3.

**DynamoDB** — banco NoSQL que armazena agendamentos, prontuários, disponibilidades e usuários como documentos JSON.

**S3 Bucket** — armazena documentos clínicos, anexos de prontuário e o modelo de IA em formato ONNX quantizado, com CORS habilitado para leitura pelo navegador.

**SES** — envia notificações automáticas de confirmação, lembrete (24h antes) e cancelamento de consultas por e-mail.

### Fluxo principal (baseado no Manual do Processo de Atendimento dos Serviços Especializados — PROAE/UFERSA)

1. Discente faz login no SIFU e acessa a área de Serviços Especializados
2. O assistente de IA (Transformers.js + modelo ONNX) realiza triagem no navegador: o discente descreve brevemente sua demanda e o modelo sugere o serviço mais adequado (Psicologia, Serviço Social, Nutrição, Odontologia ou Orientação Pedagógica), com justificativa
3. Discente confirma o serviço sugerido ou seleciona outro manualmente e escolhe data/horário disponível → Lambda CRUD salva o agendamento no DynamoDB com status `PENDENTE`
4. SES notifica o profissional responsável sobre nova solicitação → profissional confirma ou recusa → status atualizado para `CONFIRMADO` ou `CANCELADO_PROFISSIONAL`
5. SES envia confirmação ao discente e lembrete automático 24h antes da consulta
6. No dia do atendimento: profissional registra a evolução no prontuário via Lambda CRUD → status muda para `REALIZADO`; se discente não comparece, status muda para `FALTOU`
7. Profissional pode criar encaminhamento interno (para outro serviço especializado) ou externo (rede de saúde municipal) diretamente no sistema
8. Ao final do atendimento, discente recebe convite automático por e-mail para avaliar o serviço
9. Gestão PROAE/COAE acessa painel de indicadores com dados consolidados de todos os atendimentos realizados

---

## 3. Telas do sistema

### Telas do Discente

- **Dashboard** — visão geral de consultas agendadas, próximos atendimentos, encaminhamentos pendentes e histórico recente
- **Solicitar Atendimento** — assistente de IA para triagem + seleção de serviço + escolha de data/horário disponível
- **Meus Agendamentos** — lista de todos os agendamentos com status, opções de reagendamento e cancelamento
- **Histórico de Atendimentos** — registro de todas as sessões realizadas (sem acesso ao conteúdo clínico do prontuário)
- **Avaliar Atendimento** — formulário de avaliação pós-consulta

### Telas do Profissional (Técnico-Administrativo)

- **Minha Agenda** — visualização diária e semanal dos atendimentos agendados e confirmados
- **Gerenciar Disponibilidade** — configuração dos horários disponíveis, bloqueios e capacidade por turno
- **Registrar Evolução** — prontuário do atendimento: registro de evolução, encaminhamentos e retorno agendado
- **Histórico do Discente** — acesso ao histórico completo de atendimentos anteriores do discente em todos os serviços (para encaminhamentos internos)
- **Relatório de Produção** — relatório mensal de atendimentos realizados, por tipo de demanda

### Telas da Gestão PROAE / COAE

- **Painel Gerencial** — indicadores de todos os serviços: taxa de ocupação, total de atendimentos, perfil dos discentes, tempo médio de espera, avaliações
- **Gerenciar Profissionais** — cadastro, vínculo e desativação de profissionais por serviço e campus
- **Relatórios** — exportação de dados para prestação de contas institucional

---

## 4. IA generativa no projeto

O projeto utiliza IA generativa diretamente no frontend para realizar a **triagem inicial** da demanda do discente, identificando o serviço especializado mais adequado à sua necessidade antes do primeiro contato com o profissional, e para **detectar situações de urgência** que requerem atenção prioritária.

**Modelo:** fine-tuning do `flan-t5-small` (Google) sobre as descrições, critérios de elegibilidade e tipos de demanda dos cinco Serviços Especializados da PROAE/UFERSA, conforme documentados no Manual do Processo de Atendimento dos Serviços Especializados e no Portfólio de Processos da UFERSA.

**Formato:** o modelo fine-tunado é exportado para **ONNX int8 quantizado** (~30–50 MB), hospedado no S3, e executado no navegador via **Transformers.js** (ONNX Runtime Web). Toda a inferência acontece no dispositivo do usuário — nenhuma chamada de API é feita para a triagem.

**Fine-tuning:** realizado no Google Colab com a biblioteca `transformers` da Hugging Face e exportação via `optimum`.

**Como o usuário interage:** ao solicitar um atendimento, o discente descreve em linguagem natural sua necessidade (ex: "estou tendo dificuldades de concentração nos estudos e me sentindo ansioso"). O assistente analisa o texto e responde com a sugestão do serviço mais adequado e a justificativa (ex: "Com base na sua descrição, o Serviço de Psicologia pode ser o mais indicado para você, pois atua com questões emocionais e de saúde mental que impactam o desempenho acadêmico. Você também pode considerar a Orientação Pedagógica para estratégias de estudo"). Se a IA identificar palavras ou expressões associadas a situações de crise (sofrimento intenso, pensamentos de autolesão), sinaliza imediatamente para que o sistema ofereça o contato direto da equipe de Psicologia para atendimento de urgência.

**Benefício direto:** redução do tempo de espera causado por agendamentos no serviço errado, melhora na triagem de casos urgentes e ampliação do acesso aos serviços para discentes dos campi avançados que não conhecem bem as atribuições de cada serviço.

---

## 5. Conta do GitHub

- **Nome do repositório:** `web-2026-1-Maria-Karoline`
- **Usuário GitHub:** *(Mkaroline)*
- **Link do repositório:** *(https://github.com/Mkaroline/Web-2026-1-Maria-Karoline)*

---

## 6. Diagramas

*(Diagramas de arquitetura e de classes a serem inseridos após confecção no app.diagrams.net)*

### Diagrama de Arquitetura — descrição textual

```
[Navegador / React + Transformers.js]
        |
   [Route 53 / DNS]
        |
   [API Gateway]  ←→  [Cognito (Auth)]
        |
   ┌────┴────┐
   ▼          ▼
[Lambda CRUD]   [Lambda OBJ]
   |                  |
[DynamoDB]         [S3 Bucket]
                (prontuários, laudos,
                 modelo ONNX)
        |
      [SES]
(notificações por e-mail)
```

### Diagrama de Classes — entidades principais

