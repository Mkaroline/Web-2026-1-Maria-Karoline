# Descrição do Projeto Sistema de Serviços Especializados PROAE — SIFU/UFERSA

Módulo do **Sistema Integrado Funcional e Unificado (SIFU)** da UFERSA que digitaliza e centraliza o agendamento, atendimento e acompanhamento dos Serviços Especializados da Pró-Reitoria de Assuntos Estudantis (PROAE): **Psicologia, Serviço Social, Nutrição, Odontologia e Orientação Pedagógica**.

---

## Problema

<p align="justify"> Os Serviços Especializados da PROAE operam de forma manual e descentralizada. Discentes precisam se deslocar fisicamente à PROAE ou às COAEs dos campi avançados, buscar informações por canais informais (e-mail, WhatsApp) e preencher fichas em papel — sem nenhum canal digital unificado para agendamento, histórico de atendimentos ou notificações automáticas.</p>

<p align="justify"> Do lado dos profissionais, não há ferramenta centralizada para gerenciar agendas, registrar prontuários ou gerar relatórios de produção. A ausência de dados consolidados dificulta a tomada de decisão sobre alocação de recursos e identificação de perfis de vulnerabilidade estudantil.</p>

---

## Solução

Aplicação web que digitaliza todo o fluxo — do agendamento até o prontuário — com um **assistente de IA embarcado no browser** que realiza a triagem inicial da demanda do discente, sugere o serviço mais adequado e detecta situações de urgência antes mesmo do primeiro contato com o profissional

---

## Funcionalidades

- Agendamento online com triagem por IA e seleção de data/horário disponível
- Notificações automáticas de confirmação, lembrete (24h antes) e cancelamento por e-mail (Amazon SES)
- Prontuário clínico com registro de evolução, encaminhamentos internos e externos e anexos (laudos, declarações)
- Gerenciamento de agenda por profissional: horários disponíveis, bloqueios e capacidade por turno
- Avaliação de atendimento pelo discente ao final de cada sessão
- Painel gerencial consolidado para PROAE e COAEs com indicadores de todos os campi
- Relatórios de produção mensal exportáveis para prestação de contas institucional

---

## Assistente de IA

A triagem ocorre inteiramente no dispositivo do usuário — sem chamadas a APIs externas.

| | |
|---|---|
| **Modelo base** | `flan-t5-small` (Google) com fine-tuning |
| **Domínio do fine-tuning** | Descrições, critérios de elegibilidade e tipos de demanda dos 5 serviços da PROAE/UFERSA |
| **Formato** | ONNX int8 quantizado (~30–50 MB), hospedado no S3 |
| **Execução** | Transformers.js (ONNX Runtime Web) — inferência 100% no browser |
| **Treinamento** | Google Colab · Hugging Face `transformers` · `optimum` |

**Exemplo de interação:**
> Discente: *"Estou tendo dificuldades de concentração nos estudos e me sentindo ansioso."*
> IA: *"Com base na sua descrição, o Serviço de Psicologia pode ser o mais indicado, pois atua com questões emocionais e de saúde mental que impactam o desempenho acadêmico. Você também pode considerar a Orientação Pedagógica para estratégias de estudo."*

Se a IA detectar expressões associadas a situações de crise (sofrimento intenso, pensamentos de autolesão), o sistema oferece imediatamente o contato da equipe de Psicologia para atendimento de urgência.

---

## Perfis de Usuário

| Perfil | Papel no sistema |
|---|---|
| **Discente** | Agenda atendimentos, acompanha histórico, avalia sessões, reagenda ou cancela |
| **Profissional (Técnico-Adm.)** | Gerencia agenda, registra prontuários e evoluções, realiza encaminhamentos |
| **Gestão PROAE** | Acessa painel gerencial consolidado, gerencia profissionais, exporta relatórios |
| **COAE (campi avançados)** | Visualiza indicadores locais, encaminha discentes para a PROAE central |

---

## Fluxo do Atendimento

```
Login do discente
  → Triagem por IA (browser) → sugestão do serviço
  → Seleção de data/horário → Agendamento PENDENTE
  → Notificação ao profissional
  → Profissional confirma → CONFIRMADO  (ou recusa → CANCELADO_PROFISSIONAL)
  → Lembrete automático 24h antes
  → Atendimento realizado → REALIZADO  (ou FALTOU)
  → Registro de evolução + encaminhamento (opcional)
  → Convite de avaliação ao discente
  → Dados consolidados no painel da PROAE/COAE
```

**Status do agendamento:** `PENDENTE` · `CONFIRMADO` · `CANCELADO_DISCENTE` · `CANCELADO_PROFISSIONAL` · `REALIZADO` · `FALTOU`

---

## Arquitetura

Arquitetura **serverless** na AWS. O frontend React roda no browser; o backend é composto por funções Lambda acionadas via API Gateway. A IA de triagem executa inteiramente no cliente, sem Lambda dedicada.

```
┌────────────────────────────────────────────────────┐
│            CLIENT (Navegador)                      │
│  React · TypeScript · Tailwind · Transformers.js   │
└─────────────────────┬──────────────────────────────┘
                      │ HTTPS
                   Route 53
                      │
             API Gateway + Cognito
             ┌────────┴────────┐
        Lambda CRUD       Lambda OBJ
             │                 │
          DynamoDB      S3 (anexos + modelo ONNX)
             │
         SES (notificações)
```

**Stack frontend:** React · TypeScript · Tailwind CSS · React Router DOM · React Hook Form · Zod · Axios · Motion · React Icons · Transformers.js

**Stack backend (AWS):** Lambda · API Gateway · Cognito · DynamoDB · S3 · SES · Route 53

---

## Repositório

| | |
|---|---|
| **Usuário GitHub** | [Mkaroline](https://github.com/Mkaroline) |
| **Repositório** | [Web-2026-1-Maria-Karoline](https://github.com/Mkaroline/Web-2026-1-Maria-Karoline) |



