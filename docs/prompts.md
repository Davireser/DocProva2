# Histórico de Prompts — Plataforma de Streaming

> **Formato:** append-only, mais recente no topo.  
> **Numeração:** P-01, P-02, P-03 …  
> **Campos:** Data · Modelo · Prompt literal · Saída produzida · Seções atualizadas no SDD.

---

## [P-08] — Self-Correction Loop (Revisão Final v1.0)

**Data:** 2026-09-25 14:28 (BRT)  
**Modelo:** Claude Opus 5.5

### Prompt

> Execute um Self-Correction Loop sobre todo o SDD gerado:
> 1. Monte um checklist de completude: RF/RNF/RN, casos de uso (herança, include, extend), classes, DER, objetos, BCE, sequência, estados, atividades, componentes, DDD, árvore src/, plano TDD.
> 2. Verifique a consistência cruzada (nomes de classes iguais em todos os diagramas, multiplicidades batendo com o DER, estados cobertos por testes).
> 3. Liste as falhas encontradas, corrija-as e só então entregue o SDD.md final completo, em Markdown com todos os diagramas Mermaid.

### Saída Produzida

- **Checklist de completude** com 19 itens (C01–C19), com a situação antes e depois.
- **13 verificações de consistência cruzada** (V01–V13), manuais e por script: nomes das classes entre os diagramas, multiplicidades das classes × DER (15/15), operações da sequência × classes, enums, transições e estados × testes (17/17 e 6/6), UCs × componentes (16/16), Regra de Dependência (40 arestas, 0 violações), ativações da sequência, rastreabilidade de RF/RNF/RN (41/41) e sintaxe dos rótulos.
- **29 falhas encontradas e corrigidas no local (F01–F29).** As principais:
  - Multiplicidades divergentes entre classes e DER; DER sem PK na tabela N:M, sem UK e sem colunas para as listas de `Video`.
  - Durações incoerentes no Diagrama de Objetos.
  - Violação do BCE (Boundary → Entity), agora resolvida com `ProgressoController`.
  - `StatusSessao` divergente e pendência P01 (operações ausentes) resolvida.
  - Decisão incompleta no Diagrama de Atividades.
  - 6 UCs sem componente.
  - Enums sem definição; `<<choice>>` sem teste.
  - Tabelas de Decisões de Modelagem ausentes (regra RAG).
  - `\n` inválido em 22 rótulos Mermaid.
  - Envio de progresso incompatível com o RNF10.
- **Tabelas novas:** rastreabilidade de RNF (8.4), comparação de multiplicidades (8.5), resultados das verificações (8.6) e limitações remanescentes (8.7).
- Metadados → **v1.0**, status "Revisado — versão final". Glossário: *DTO*, *LGPD* e *PCI-DSS*.

### Seções Atualizadas no SDD

- Metadados, Histórico de Versões e Glossário
- Seção 1 — 1.5 e 1.6 (rótulos, RF10, decisões)
- Seção 2 — 2.1, 2.2 e 2.3 (multiplicidades, operações, DER, objetos, decisões)
- Seção 3 — 3.1–3.4 e nova 3.5 (BCE, sequência, decisões)
- Seção 5 — 5.3 (ramo `D0`, FA-11)
- Seção 6 — 6.2–6.5 (novos componentes, eventos, VOs, recontagem)
- Seção 7 — 7.3, 7.6 e 7.7.3 (`StreamInfo`, enumerações, árvore, TC-AS-11)
- Seção 8 — Self-Correction Loop (Revisão Final v1.0) *(nova)*

---

## [P-07] — Mapeamento Arquitetural DDD + Clean Architecture e Plano TDD

**Data:** 2026-09-25 14:20 (BRT)  
**Modelo:** Claude Opus 5.5

### Prompt

> Faça o mapeamento arquitetural DDD + Clean Architecture:
> 1. Especifique os Aggregates (e seus Aggregate Roots), os Value Objects imutáveis (QualidadeVideo, Plano), os Repositórios (interfaces no domínio) e o Domain Service ValidadorTelasSimultaneas.
> 2. Detalhe a árvore de pastas src/ (domain, application, infrastructure, presentation).
> 3. Defina o Plano de Testes TDD: casos Red-Green-Refactor para os Value Objects, para o ValidadorTelasSimultaneas e para as transições de estado da Assinatura.

### Saída Produzida

- **4 Bounded Contexts:** Conta, Assinatura, Reprodução e Catálogo.
- **10 Aggregates** com seus Aggregate Roots, em `classDiagram` com estereótipos (`<<AggregateRoot>>`, `<<Entity>>`, `<<ValueObject>>`, `<<DomainService>>`) e referências por ID entre aggregates. Tabela de invariantes por aggregate.
- **Value Objects imutáveis:**
  - `QualidadeVideo` (HDR exige 4K).
  - `Plano`, com fábricas `basico()`, `standard()` e `premium()`; `permiteOffline` e `permiteHDR` são derivados.
  - `Dinheiro`, `Email` e `ResultadoValidacaoTelas`.
  - Esqueletos TypeScript de `QualidadeVideo` e `Plano`.
- **10 interfaces de repositório no Domain**, uma por Aggregate Root, com métodos. `SessaoRepository.listarAtivasPorConta` resolve parte da pendência P01.
- **Domain Service `ValidadorTelasSimultaneas`:** algoritmo RN05/RN06, que ignora o mesmo dispositivo e revoga várias sessões após um downgrade, com assinatura TypeScript. Também os demais Domain Services e 6 eventos de domínio.
- **Árvore `src/`** organizada por camada e, dentro dela, por Bounded Context; `main.ts` como Composition Root; árvore de `tests/`.
- **Plano de Testes TDD** com 41 casos Red-Green-Refactor:
  - 15 de Value Objects (TC-QV, TC-PL, TC-DI).
  - 9 do `ValidadorTelasSimultaneas` (TC-VT).
  - 17 das transições T01–T17 da `Assinatura` (TC-AS), incluindo guardas proibidas e a tabela `estaVigente`. O refactor principal é o padrão State.
  - Pirâmide de testes.
- **Revisão da Seção 6:** repositórios movidos da Application para o Domain e `PoliticaStreamsSimultaneos` → `ValidadorTelasSimultaneas`. A Regra de Dependência foi verificada de novo: 32 arestas, 0 violações.
- **Refinamentos R01–R07** em relação às Seções 2 e 6.
- Glossário: *Aggregate*, *Aggregate Root*, *Bounded Context*, *Domain Service*, *TDD* e *Value Object*.

### Seções Atualizadas no SDD

- Glossário (novos termos)
- Seção 6 — 6.1, 6.2, 6.3, 6.4, 6.5 e 6.6 (revisão marcada *v0.7*)
- Seção 7 — Mapeamento Arquitetural DDD + Clean Architecture *(nova)*

---

## [P-06] — Passo C: Diagrama de Componentes (Clean Architecture)

**Data:** 2026-09-25 14:15 (BRT)  
**Modelo:** Claude Opus 5.5

### Prompt

> Passo C da cadeia. Consolidando os passos A e B, crie o Diagrama de Componentes (flowchart com subgraphs) seguindo Clean Architecture: Domain, Application (Use Cases), Infrastructure e Presentation, respeitando a Regra de Dependência (setas apontando para dentro).

### Saída Produzida

- **Visão geral** (`flowchart LR`) das 4 camadas com as setas da Regra de Dependência.
- **Diagrama de Componentes detalhado** (`flowchart TB` com subgraphs aninhados, com Domain dentro de Application para representar o núcleo):
  - **Presentation:** `VideoSelectionPage`, `PlayerUI`, `AssinaturaPage`, `StreamingAPI`, `ProgressoAPI`, `AssinaturaAPI`, `PagamentoWebhook` e `AgendadorCobrancaJob`.
  - **Application:** os controles BCE do Passo A (`ReproducaoController`, `SessaoValidator`, `StreamLimitController`, `QualidadeSelector`), mais `ProgressoController` e os controllers do Passo B (`AssinaturaController`, `RenovacaoController`, `CobrancaController`); DTOs; portas de saída (repositórios, `CDNGateway`, `DRMGateway`, `PagamentoGateway`, `NotificacaoGateway`, `TokenService`).
  - **Domain:** entidades da Seção 2.1, políticas de domínio (streams, qualidade, download, cobrança), enums e eventos de domínio.
  - **Infrastructure:** repositórios SQL, `RedisSessaoRepository`, `CDNAdapter`, `DRMLicenseAdapter`, `PagamentoGatewayAdapter`, `NotificacaoAdapter` e `TokenCryptoAdapter`, todos implementando portas.
- **Verificação automatizada da Regra de Dependência:** 31 arestas, 0 violações.
- **Catálogo de componentes** (camada, origem no Passo A/B, responsabilidade, rastreabilidade).
- **Tabela de consolidação**: cada mensagem do Diagrama de Sequência (Passo A) e cada transição T01–T17 (Passo B) mapeada ao caminho pelos componentes.
- **Decisões de modelagem**, incluindo a situação das pendências P01 (parcialmente resolvida) e P02 (resolvida) da Seção 4.
- Glossário: *Clean Architecture*, *Porta (Port)* e *Regra de Dependência*; termos reordenados alfabeticamente.

### Seções Atualizadas no SDD

- Glossário (novos termos e ordenação)
- Seção 6 — Passo C: Diagrama de Componentes (Clean Architecture) *(nova)*

---

## [P-05] — Passo B: Diagrama de Estados e Diagrama de Atividades

**Data:** 2026-09-25 14:11 (BRT)  
**Modelo:** Claude Opus 5.5

### Prompt

> Passo B da cadeia. Usando as entidades do Passo A:
> 1. Diagrama de Estados (stateDiagram-v2) da Assinatura: Ativa -> Suspensa -> Cancelada (com transições, eventos e guardas, ex.: pagamento recusado, reativação).
> 2. Diagrama de Atividades (flowchart) do processo de assinatura/renovação, com decisões e fluxos alternativos.

### Saída Produzida

- **Diagrama de Estados** (`stateDiagram-v2`) da `Assinatura`: `Pendente → Ativa → Suspensa → Cancelada`.
  - `Ativa` é estado composto com os subestados `EmDia` e `EmCarencia`.
  - `Cancelada` é estado composto com `<<choice>>` entre `AcessoRemanescente` e `SemAcesso`.
  - 17 transições (T01–T17) em tabela, com evento, guarda, ação e regra. Inclui os eventos temporais `after(30 dias)` e `after(90 dias)`, reativação a partir de `Suspensa` e de `Cancelada`, e upgrade/downgrade como autotransições.
- **Tabela `StatusAssinatura`** × estado × `estaVigente()` × acesso a reprodução e download, ligando este diagrama ao Alt-6 do Passo A.
- **Diagrama de Atividades** (`flowchart`):
  - 2 raias (Assinante/Visitante e Sistema de Cobrança) e 2 nós de início (contratação e evento temporal de renovação).
  - Subprocesso de cobrança único, que trata a recusa de acordo com a **origem** (nova assinatura, renovação ou reativação), e fork/join para comprovante e liberação de benefícios.
  - 10 fluxos alternativos (FA-01–FA-10).
- **Rastreabilidade** trecho × UC × RF × RN × entidades, e **decisões de modelagem**.
- **Complementos de consistência** (as regras de cobrança do Ramo A foram absorvidas):
  - RF15–RF16 e RN10–RN15 na Seção 1.4.
  - UC12–UC16 no diagrama de casos de uso (1.5) e na rastreabilidade (1.6); UC14–UC16 incluem UC02.
  - `Assinatura` ganhou `tentativasCobranca`, `dataSuspensao`, `suspender()`, `reativar()` e a associação `downgrade agendado` (2.1), com as colunas correspondentes no DER (2.2).
  - Glossário: *Carência* e *Pro-rata*.

### Seções Atualizadas no SDD

- Seção 1 — 1.4, 1.5 e 1.6 (acréscimos marcados *v0.5*)
- Seção 2 — 2.1 e 2.2 (acréscimos em `Assinatura`)
- Seção 5 — Passo B: Diagrama de Estados e Diagrama de Atividades (Assinatura) *(nova)*

---

## [P-04] — Revisão de Consistência (Errata v0.4)

**Data:** 2026-09-25 14:01 (BRT)  
**Modelo:** Claude Opus 5.5

### Prompt

> corrija oq vc julgar nescessario antes de continuarmos

### Saída Produzida

- **8 correções (E01–E08)** aplicadas no próprio local, sem remover seções:
  - Diagrama de sequência (3.2): ativação desbalanceada de `SessaoValidator` que quebrava a renderização; sessão expirada deixa de ser renovada (RN07); novo passo `Assinatura.estaVigente()` que fornece o `planoId` (RN09); RN06 passa a revogar a `Sessao (mais antiga)` em vez da sessão atual.
  - Diagrama de classes/objetos (2.1, 2.3): `Assinante` passa a ser concreta, sem atributos duplicados, com associação `Assinante "1" --> "1" ContaAssinante`; `+estaVigente()` em `Assinatura`; objeto `titular1 : AssinantePremium`.
  - Casos de uso (1.5, 1.6): seta de herança dos atores corrigida e lista de `«include»` do UC02 explicitada.
- Tabelas 3.1, 3.3 e 3.4 atualizadas (novo Alt-6 — assinatura não vigente).
- **2 pendências (P01–P02)** registradas para as próximas etapas.
- Metadados → v0.4; glossário ganhou o termo *Sliding TTL*.

### Seções Atualizadas no SDD

- Seção 1 — 1.5 e 1.6 (correções pontuais)
- Seção 2 — 2.1 e 2.3 (correções pontuais)
- Seção 3 — 3.1, 3.2, 3.3 e 3.4 (correções pontuais)
- Seção 4 — Revisão de Consistência (Errata v0.4) *(nova)*

---

## [P-03] — Passo A: BCE + Diagrama de Sequência

**Data:** 2026-09-25 13:47 (BRT)  
**Modelo:** Claude Sonnet 4.6 (Thinking)

### Prompt

> Passo A da cadeia. Com base no diagrama de classes anterior:
> 1. Crie a Tabela BCE (Boundary, Control, Entity) do caso de uso "Reproduzir Vídeo".
> 2. Crie o Diagrama de Sequência (sequenceDiagram) do fluxo de dar Play no vídeo, incluindo Validar Sessão e verificação de telas simultâneas.

### Saída Produzida

- **Tabela BCE** com 16 elementos classificados em 4 Boundary, 4 Control e 8 Entity, com estereótipo UML, responsabilidade e classe de origem.
- **Mapa BCE visual** em ASCII art com três camadas.
- **Diagrama de Sequência** (`sequenceDiagram` com `autonumber` e boxes por camada) cobrindo 8 trechos: seleção do vídeo, `«include»` Validar Sessão (com alt EXPIRADA/INVÁLIDA), verificação de streams simultâneos (com alt limite atingido / RN06), seleção de qualidade ABR+HDR, obtenção de manifesto HLS/DASH via CDN, Continue Watching, inicialização do player e loop assíncrono de progresso a cada 30s.
- **Tabela de rastreabilidade** BCE × RF × RN por trecho.
- **5 fluxos alternativos** documentados.

### Seções Atualizadas no SDD

- Seção 3 — Passo A: BCE + Diagrama de Sequência (UC04)

---

## [P-02] — Fase 2: Diagramas de Classes, DER e Objetos

**Data:** 2026-09-25 13:43 (BRT)  
**Modelo:** Claude Sonnet 4.6 (Thinking)

### Prompt

> [CONTEXTO RAG — regras do documento de software]
> - Todo diagrama deve ser em Mermaid válido.
> - Classes com atributos tipados e visibilidade (+, -, #).
> - Relacionamentos com multiplicidade explícita nos dois lados.
> - Composição (*--) quando a parte não existe sem o todo; agregação (o--) quando existe independente.
> - DER com PK, FK e tipos SQL.
>
> [EXEMPLOS FEW-SHOT]
> Exemplo de composição:
> ContaAssinante "1" *-- "1..5" Perfil : possui
>
> Exemplo de agregação:
> Catalogo "1" o-- "0..*" Video : agrupa
>
> Usando o contexto e os exemplos acima, gere:
> 1. Diagrama de Classes (classDiagram) com composição, agregação e multiplicidades.
> 2. DER (erDiagram) com tabelas, PKs e FKs.
> 3. Diagrama de Objetos: uma ContaAssinante com múltiplos perfis ativos, com valores concretos.

### Saída Produzida

- **Diagrama de Classes** com 13 classes tipadas (visibilidade +/-/#), herança `AssinantePremium --|> Assinante`, 7 composições, 2 agregações e 4 associações com multiplicidade explícita em ambas as extremidades.
- **DER** com 12 tabelas e tipos SQL (UUID, VARCHAR, DECIMAL, TIMESTAMP, BOOLEAN, TEXT, BIGINT), incluindo tabela associativa `CATALOGO_VIDEO` para o N:M, com PKs, FKs e UK explícitos.
- **Diagrama de Objetos** do assinante `joao.silva@gmail.com` (Plano Premium): 3 perfis (João, Maria, Ana infantil), 1 sessão ativa na Smart TV Samsung 4K HDR, 2 progressos (Interestelar 52,7% e Oppenheimer 100%), 2 downloads offline (1080p e 4K), 1 forma de pagamento.
- **Tabela de decisões de modelagem** com justificativas para cada escolha de composição vs. agregação e para o uso de UUID como PK universal.

### Seções Atualizadas no SDD

- Seção 2 — Diagramas de Classes, DER e Objetos

---

## [P-01] — Fase 1: Tree-of-Thought de Requisitos e Casos de Uso

**Data:** 2026-09-25 13:38 (BRT)  
**Modelo:** Claude Sonnet 4.6 (Thinking)

### Prompt

> Atue como Arquiteto de Streaming, especialista em plataformas de Streaming de Mídia. Use raciocínio criativo e divergente (temperatura alta).
>
> Domínio: Gestão de Assinaturas e Reprodução sob Demanda.
>
> Use Tree-of-Thought para mapear Requisitos e Casos de Uso:
> 1. Gere 3 ramos distintos de interpretação do sistema (ex.: foco em monetização, foco em experiência de reprodução, foco em multi-dispositivo).
> 2. Para cada ramo, liste Requisitos Funcionais (RF), Não Funcionais (RNF) e Regras de Negócio (RN), e os Casos de Uso correspondentes.
> 3. Avalie cada ramo com critérios explícitos (completude, viabilidade, aderência ao domínio, corretude UML), com nota de 1 a 5.
> 4. Realize a poda dos ramos fracos, justificando, e consolide o ramo vencedor.
>
> O ramo final deve obrigatoriamente conter:
> - Herança de atores: AssinantePremium --|> Assinante
> - <<include>>: "Validar Sessão" nos casos que exigem autenticação
> - <<extend>>: "Download Offline" estendendo "Reproduzir Vídeo" (somente Premium)
>
> Saída:
> - Tabela de RF/RNF/RN numerados (RF01, RNF01, RN01)
> - Diagrama de Casos de Uso em Mermaid
> - Tabela Caso de Uso × Ator × RF relacionado

### Saída Produzida

- **3 ramos ToT** gerados: Ramo A (Monetização), Ramo B (Reprodução), Ramo C (Multi-Dispositivo) — cada um com RF, RNF, RN e UCs próprios.
- **Scoring** em tabela com 4 critérios (peso 25% cada): Ramo B venceu com score perfeito 5,00; Ramos A e C empataram em 3,50.
- **Poda justificada** de A (baixa aderência ao domínio VOD; include/extend forçados) e C (foco em infraestrutura, sem core VOD; extend forçado).
- **Consolidação final** (Ramo B + absorção seletiva de A e C): 14 RF, 10 RNF, 9 RN numerados.
- **Diagrama de Casos de Uso** (Mermaid `flowchart TD`) com herança `AssinantePremium --|> Assinante`, 5 `«include»` em UC02 (Validar Sessão) e `«extend»` de UC05 (Download Offline) sobre UC04.
- **Tabela de rastreabilidade** UC × Ator × RF com 11 casos de uso mapeados.

### Seções Atualizadas no SDD

- Seção 1 — Tree-of-Thought: Requisitos e Casos de Uso
