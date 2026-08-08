# FDT — Modelo de Dados e Schemas v0.1

**Status:** Draft  
**Versão:** 0.1  
**Domínio:** Framework de Descoberta Tecnológica — FDT  
**Tipo:** Especificação de Dados  
**Repositório:** `ghosolutions/4gaKanban`  
**Dependência:** FDT — Especificação Funcional v0.1

---

## 1. Objetivo

Definir o modelo canônico de dados do Framework de Descoberta Tecnológica — FDT, estabelecendo entidades de domínio, relacionamentos, identificadores, estados, enums, schemas de entrada e saída, estrutura de evidências, modelo de avaliação e critérios mínimos de validação.

O modelo deve permitir que uma descoberta realizada manualmente, via IA, GitHub, APIs ou workflows automatizados seja representada de forma uniforme e auditável.

## 2. Princípios do Modelo

### 2.1 Fonte antes da conclusão
Toda informação factual relevante sobre uma solução deve, quando possível, possuir uma evidência associada.

### 2.2 Dado observado ≠ dado inferido
O modelo distingue:
- `observed` — obtido diretamente da fonte;
- `derived` — calculado pelo FDT;
- `inferred` — inferido por agente ou regra;
- `user_provided` — informado pelo usuário.

### 2.3 Histórico preservado
Métricas mutáveis devem ser armazenadas temporalmente, evitando sobrescrever fatos anteriores.

### 2.4 Schema versionado
Todo artefato estruturado deve possuir `schema_version`.

## 3. Entidades Canônicas

- `FDTProject`
- `Requirement`
- `DiscoveryRun`
- `SearchQuery`
- `SearchResult`
- `Candidate`
- `Solution`
- `Repository`
- `MetricSnapshot`
- `LicenseInfo`
- `Capability`
- `DeploymentOption`
- `Evidence`
- `Criterion`
- `Evaluation`
- `CriterionScore`
- `Risk`
- `Comparison`
- `Decision`
- `Artifact`

## 4. Schemas Lógicos

### 4.1 FDTProject

```yaml
FDTProject:
  id: string
  schema_version: string
  name: string
  description: string
  objective: string
  project_type: string
  lifecycle_stage: string
  estimated_users: integer|null
  platforms:
    - string
  monetization:
    - string
  constraints:
    - string
  created_at: datetime
  updated_at: datetime
```

### 4.2 Requirement

```yaml
Requirement:
  id: string
  project_id: string
  title: string
  description: string
  type:
    enum:
      - functional
      - non_functional
      - technical
      - security
      - operational
      - compliance
      - infrastructure
  priority:
    enum:
      - must
      - should
      - could
      - wont
  status:
    enum:
      - proposed
      - approved
      - rejected
      - deferred
  acceptance_criteria:
    - string
```

### 4.3 DiscoveryRun

```yaml
DiscoveryRun:
  id: string
  project_id: string
  objective: string
  status:
    enum:
      - planned
      - running
      - completed
      - failed
      - cancelled
  started_at: datetime
  completed_at: datetime|null
  sources:
    - string
  parameters: object
  created_by:
    type: string
    identifier: string
```

### 4.4 SearchQuery

```yaml
SearchQuery:
  id: string
  discovery_run_id: string
  source: string
  query: string
  query_type:
    enum:
      - repository
      - code
      - issue
      - pull_request
      - discussion
      - user
      - commit
      - package
      - wiki
      - topic
      - web
  filters: object
  executed_at: datetime
```

### 4.5 SearchResult

```yaml
SearchResult:
  id: string
  discovery_run_id: string
  search_query_id: string
  external_id: string|null
  source: string
  title: string
  url: uri
  description: string|null
  raw_metadata: object
  collected_at: datetime
```

### 4.6 Candidate

```yaml
Candidate:
  id: string
  discovery_run_id: string
  search_result_id: string|null
  solution_id: string|null
  relevance_score:
    type: number
    minimum: 0
    maximum: 100
  status:
    enum:
      - discovered
      - shortlisted
      - rejected
      - promoted
  rejection_reason: string|null
```

### 4.7 Solution

```yaml
Solution:
  id: string
  name: string
  slug: string
  description: string
  solution_type:
    enum:
      - application
      - platform
      - service
      - library
      - framework
      - infrastructure
      - developer_tool
      - protocol
      - dataset
      - other
  categories:
    - string
  homepage_url: uri|null
  documentation_url: uri|null
  open_source: boolean|null
  lifecycle_status:
    enum:
      - active
      - maintenance
      - inactive
      - archived
      - unknown
  first_seen_at: datetime
  last_verified_at: datetime|null
```

### 4.8 Repository

```yaml
Repository:
  id: string
  solution_id: string
  provider:
    enum:
      - github
      - gitlab
      - codeberg
      - self_hosted
      - other
  owner: string
  name: string
  url: uri
  default_branch: string|null
  primary_language: string|null
  archived: boolean|null
  fork: boolean|null
  created_at_source: datetime|null
  updated_at_source: datetime|null
  pushed_at: datetime|null
```

### 4.9 MetricSnapshot

```yaml
MetricSnapshot:
  id: string
  repository_id: string
  collected_at: datetime
  stars: integer|null
  forks: integer|null
  watchers: integer|null
  open_issues: integer|null
  contributors: integer|null
  commits_30d: integer|null
  commits_90d: integer|null
  releases_total: integer|null
  latest_release_at: datetime|null
  days_since_last_commit: integer|null
```

### 4.10 LicenseInfo

```yaml
LicenseInfo:
  id: string
  solution_id: string
  identifier: string
  name: string
  osi_approved: boolean|null
  copyleft:
    enum:
      - none
      - weak
      - strong
      - network
      - unknown
  commercial_use:
    enum:
      - allowed
      - restricted
      - prohibited
      - unknown
  modification:
    enum:
      - allowed
      - restricted
      - prohibited
      - unknown
  redistribution:
    enum:
      - allowed
      - restricted
      - prohibited
      - unknown
  white_label_risk:
    enum:
      - low
      - medium
      - high
      - unknown
```

### 4.11 Capability

```yaml
Capability:
  id: string
  solution_id: string
  key: string
  name: string
  description: string|null
  support_level:
    enum:
      - native
      - plugin
      - integration
      - partial
      - planned
      - unsupported
      - unknown
```

### 4.12 DeploymentOption

```yaml
DeploymentOption:
  id: string
  solution_id: string
  deployment_type:
    enum:
      - binary
      - package
      - docker
      - docker_compose
      - kubernetes
      - helm
      - source
      - mobile_apk
      - cloud
      - other
  supported: boolean|null
  official: boolean|null
  reference_url: uri|null
```

### 4.13 Evidence

```yaml
Evidence:
  id: string
  entity_type: string
  entity_id: string
  field: string|null
  source_type:
    enum:
      - repository
      - documentation
      - website
      - api
      - issue
      - pull_request
      - discussion
      - release
      - community
      - user
      - generated
  source_url: uri|null
  collected_at: datetime
  value: any
  provenance:
    enum:
      - observed
      - derived
      - inferred
      - user_provided
  confidence:
    enum:
      - high
      - medium
      - low
  excerpt: string|null
```

### 4.14 Criterion

```yaml
Criterion:
  id: string
  key: string
  name: string
  description: string
  category: string
  weight:
    type: number
    minimum: 0
    maximum: 1
  scoring_method:
    enum:
      - boolean
      - numeric
      - scale
      - formula
      - manual
```

### 4.15 Evaluation

```yaml
Evaluation:
  id: string
  project_id: string
  solution_id: string
  evaluation_version: string
  evaluated_at: datetime
  overall_score:
    type: number
    minimum: 0
    maximum: 100
  classification:
    enum:
      - recommended
      - conditional
      - experimental
      - rejected
      - insufficient_data
  notes: string|null
```

### 4.16 CriterionScore

```yaml
CriterionScore:
  id: string
  evaluation_id: string
  criterion_id: string
  raw_score: number|null
  normalized_score: number|null
  weighted_score: number|null
  confidence:
    enum:
      - high
      - medium
      - low
  rationale: string|null
```

### 4.17 Risk

```yaml
Risk:
  id: string
  project_id: string
  solution_id: string|null
  category:
    enum:
      - technical
      - security
      - licensing
      - operational
      - dependency
      - maturity
      - vendor
      - community
      - compliance
      - cost
  description: string
  probability:
    enum:
      - low
      - medium
      - high
  impact:
    enum:
      - low
      - medium
      - high
  severity:
    enum:
      - low
      - medium
      - high
      - critical
  mitigation: string|null
  status: string
```

### 4.18 Comparison

```yaml
Comparison:
  id: string
  project_id: string
  title: string
  solution_ids:
    - string
  criteria_ids:
    - string
  ranking:
    - solution_id: string
      position: integer
      score: number
  created_at: datetime
```

### 4.19 Decision

```yaml
Decision:
  id: string
  project_id: string
  title: string
  context: string
  status:
    enum:
      - proposed
      - accepted
      - rejected
      - superseded
  selected_solution_ids:
    - string
  rejected_solution_ids:
    - string
  rationale: string
  approved_at: datetime|null
```

### 4.20 Artifact

```yaml
Artifact:
  id: string
  project_id: string
  discovery_run_id: string|null
  artifact_type:
    enum:
      - technology_inventory
      - logical_architecture
      - physical_architecture
      - dependency_matrix
      - open_source_catalog
      - devops_plan
      - observability_plan
      - security_plan
      - operations_plan
      - roadmap
      - comparison_report
      - decision_record
      - other
  version: string
  uri: string|null
  content_hash: string|null
  generated_at: datetime
```

## 5. Relacionamentos Principais

```text
FDTProject 1 ─── N Requirement
FDTProject 1 ─── N DiscoveryRun
DiscoveryRun 1 ─── N SearchQuery
SearchQuery 1 ─── N SearchResult
DiscoveryRun 1 ─── N Candidate
Solution 1 ─── N Repository
Solution 1 ─── N Capability
Solution 1 ─── N DeploymentOption
Solution 1 ─── N MetricSnapshot
Solution 1 ─── N Evaluation
Evaluation 1 ─── N CriterionScore
FDTProject 1 ─── N Decision
```

## 6. Schema Mínimo de Entrada

```yaml
schema_version: "0.1"
project:
  name: string
  objective: string
  project_type: string|null
  estimated_users: integer|null
  platforms:
    - string
  monetization:
    - string
requirements:
  - title: string
    type: string
    priority: string
```

## 7. Schema Mínimo de Resultado

```yaml
schema_version: "0.1"
discovery:
  id: string
  project_id: string
  executed_at: datetime
solutions:
  - id: string
    name: string
    description: string
    repository:
      url: uri
      stars: integer|null
      forks: integer|null
      primary_language: string|null
      last_commit_at: datetime|null
    license:
      identifier: string|null
    capabilities:
      docker: boolean|null
      kubernetes: boolean|null
      api: boolean|null
      self_hosted: boolean|null
    evaluation:
      score: number|null
      classification: string|null
    evidence_count: integer
```

## 8. Regras de Validação

Obrigatórias:
- todo `Solution` deve possuir `name`;
- toda execução deve estar vinculada a um `FDTProject`;
- todo `CriterionScore` deve referenciar um `Criterion`;
- scores normalizados devem estar dentro da escala definida;
- URLs, quando presentes, devem ser válidas;
- datas devem seguir ISO 8601;
- todo registro deve respeitar `schema_version`.

Desejáveis:
- afirmações críticas acompanhadas de `Evidence`;
- licença acompanhada de fonte;
- métricas acompanhadas de `collected_at`;
- avaliações acompanhadas de racional;
- rejeições acompanhadas de motivo.

## 9. Identificadores

Padrão recomendado:

```text
fdt_<entity>_<uuid>
```

UUID v7 ou equivalente ordenável é recomendado para implementações futuras.

## 10. Null, Unknown e Unsupported

O FDT distingue `null/unknown` de `false/unsupported`.

Exemplo:

```yaml
docker: null
```

significa ainda não determinado, enquanto:

```yaml
docker: false
```

significa avaliado e não suportado.

## 11. Deduplicação

Ordem recomendada:
1. repository canonical URL;
2. provider + owner + repository;
3. official homepage;
4. package identifier;
5. nome normalizado como heurística.

## 12. Persistência Recomendada

Primeira implementação recomendada: PostgreSQL.

Estruturas altamente variáveis podem utilizar JSONB, especialmente:
- `SearchResult.raw_metadata`;
- `DiscoveryRun.parameters`;
- `Evidence.value`.

## 13. Critérios de Aceite v0.1

- [ ] representar projeto, requisito e descoberta;
- [ ] permitir registrar consultas e resultados;
- [ ] representar uma solução independentemente da fonte;
- [ ] suportar múltiplos repositórios;
- [ ] registrar métricas historicamente;
- [ ] registrar licença;
- [ ] registrar capacidades;
- [ ] registrar evidências;
- [ ] permitir scoring contextual;
- [ ] suportar riscos;
- [ ] permitir comparação entre alternativas;
- [ ] registrar decisões;
- [ ] permitir geração dos artefatos previstos pelo FDT;
- [ ] possuir schema mínimo de entrada;
- [ ] possuir schema mínimo de saída;
- [ ] distinguir informação desconhecida de informação negativa;
- [ ] oferecer mecanismo de versionamento.

## 14. Decisões de Design v0.1

| ID | Decisão | Estado |
|---|---|---|
| MD-001 | Separar `Solution` de `Repository` | Proposta |
| MD-002 | Tratar descoberta como execução versionável | Proposta |
| MD-003 | Preservar métricas em snapshots | Proposta |
| MD-004 | Criar entidade genérica de evidência | Proposta |
| MD-005 | Separar score bruto, normalizado e ponderado | Proposta |
| MD-006 | Utilizar JSONB somente para estruturas não canônicas | Proposta |
| MD-007 | Preservar provenance do dado | Proposta |
| MD-008 | Diferenciar `unknown` de `unsupported` | Proposta |

## 15. Fora de Escopo v0.1

Não define ainda:
- modelo físico completo PostgreSQL;
- migrations;
- índices;
- API REST;
- GraphQL;
- algoritmo definitivo de scoring;
- embeddings;
- autenticação;
- RBAC;
- ETL;
- conectores GitHub;
- crawling;
- interface de usuário;
- schemas específicos por provedor.

## 16. Próximo Gate

**Gate MD-01 — Aprovação do Modelo Conceitual**

Próximos artefatos após aprovação:
1. `FDT — JSON Schema Canônico v0.1`
2. `FDT — Modelo Relacional PostgreSQL v0.1`
