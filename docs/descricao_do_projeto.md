# Descrição do Projeto - SPA-UFERSA

## 1. Problema a Ser Solucionado

O processo de **Atualização dos Projetos Pedagógicos dos Cursos de Graduação (PPCs)** na UFERSA possui 41 atividades distribuídas entre múltiplos atores, conforme manual institucional.

### Dores Identificadas:
| Dor | Impacto |
|-----|---------|
| Processo manual baseado em documentos estáticos | Retrabalho e inconsistência de dados |
| Falta de rastreabilidade de versões | Risco jurídico e pedagógico |
| Dificuldade de integração com SIGAA/SIPAC | Divergência entre PPC aprovado e cadastro operacional |
| Ausência de visibilidade em tempo real | Atrasos nas atualizações curriculares |

### Solução Proposta:
Desenvolver o **SPA-UFERSA**, módulo do SIFU para digitalizar e orquestrar o fluxo de atualização de PPCs, com:
- ✅ Controle de versão de documentos
- ✅ Notificações automáticas por etapa
- ✅ Validação assistida por IA de conformidade normativa
- ✅ Integração nativa com SIGAA, SIPAC e Boletim Interno

---

## 2. Perfis de Usuário

```plantuml
@startuml roles
left to right direction
skinparam packageStyle rectangle

actor "Coordenador de Curso" as Coord
actor "Comissão/NDE" as NDE
actor "Colegiado de Curso" as Coleg
actor "Departamento" as Dept
actor "PROGRAD/DIP" as Prograd
actor "Comitê de Graduação" as Comite
actor "CONSEPE" as Consepe
actor "SISBI" as Sisbi
actor "DRA/SIGAA" as Dra

rectangle "SPA-UFERSA" {
  usecase "Solicitar comissão" as UC1
  usecase "Editar PPC colaborativo" as UC2
  usecase "Aprovar propostas" as UC3
  usecase "Analisar conformidade" as UC4
  usecase "Emitir parecer" as UC5
  usecase "Publicar resolução" as UC6
  usecase "Validar bibliografia" as UC7
  usecase "Cadastrar no SIGAA" as UC8
}

Coord --> UC1
Coord --> UC2
NDE --> UC2
Coleg --> UC3
Dept --> UC3
Prograd --> UC4
Comite --> UC5
Consepe --> UC6
Sisbi --> UC7
Dra --> UC8

note right of Prograd
  Valida:
  - 10% de extensão
  - Carga horária mínima
  - Alinhamento com DCNs
end note

@enduml