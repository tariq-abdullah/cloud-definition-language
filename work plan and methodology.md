# Work Plan and Methodology

## Title

Design and Implementation of a Unified Cloud Architecture Definition Language: A Semantic Compiler Framework for Portable, Vendor-Neutral Infrastructure-as-Code Generation

## Purpose of This Plan

This work plan converts the existing literature study, research gap, and thesis positioning into a practical method for answering the research questions. The project is treated as a design-science research study because it produces an artifact: a vendor-neutral cloud DSL, semantic IR, provider capability model, compiler prototype, and evaluation framework.

## Central Research Problem

Existing IaC tools either expose provider-native resources or provide a common workflow over provider-specific schemas. They do not sufficiently solve semantic portability: the ability to express infrastructure intent once, translate it across providers, and explain whether the generated infrastructure preserves the intended behavior.

The thesis therefore investigates:

```text
Portable cloud intent
    -> Semantic IR
    -> Provider capability mapping
    -> Backend lowering
    -> Portability and fidelity diagnostics
```

## Research Questions

RQ1: What minimal set of cloud infrastructure concepts can express common portable architectures across multiple providers without collapsing into provider-specific resources?

RQ2: How should a semantic IR represent cloud resource intent, dependencies, constraints, capabilities, and lifecycle behavior?

RQ3: How can provider capability mappings be defined so that exact matches, partial matches, unsupported features, and degraded alternatives are distinguishable?

RQ4: How accurately can the compiler lower a single DSL specification into multiple IaC backends while preserving behavior?

RQ5: What metrics can evaluate portability, semantic fidelity, expressiveness, and explainability of generated IaC?

## Overall Methodology

The methodology follows six stages:

1. Domain analysis
2. DSL design
3. Semantic IR design
4. Provider capability mapping
5. Compiler prototype implementation
6. Evaluation and analysis

Each stage produces an artifact and feeds evidence into one or more research questions.

| Stage | Method | Output | Research questions answered |
| --- | --- | --- | --- |
| Domain analysis | Analyze literature, IaC tools, provider docs, and sample architectures | Cloud concept taxonomy | RQ1 |
| DSL design | Define syntax, semantics, constraints, and examples | Vendor-neutral DSL specification | RQ1 |
| IR design | Model infrastructure as a semantic graph | IR schema and invariants | RQ2 |
| Capability mapping | Compare provider features against IR concepts | Capability matrix and lowering rules | RQ3 |
| Compiler implementation | Build parser, validator, IR generator, backend emitters | Working prototype | RQ4 |
| Evaluation | Run benchmark cases, validate output, calculate metrics | Results and analysis | RQ4, RQ5 |

## Phase 1: Domain Analysis

### Objective

Identify the minimal cloud concepts needed for portable infrastructure representation.

### Method

Review the concepts exposed by:

- Provider-native IaC: AWS CloudFormation, Azure Bicep
- Multi-provider IaC: Terraform/OpenTofu, Pulumi
- Model-based approaches: TOSCA, CAMEL, DOML
- Platform abstractions: Crossplane, Kubernetes manifests

Extract common infrastructure concepts such as:

- Compute
- Network
- Storage
- Database
- Identity and access control
- Load balancing
- Autoscaling
- Secrets/configuration
- Regions/zones
- Lifecycle and dependency behavior

### Expected Output

A concept taxonomy that separates:

- portable concepts
- provider-specific concepts
- partially portable concepts
- non-portable concepts

### How This Answers RQ1

RQ1 is answered by showing which cloud concepts appear repeatedly across tools and providers, and which concepts should become first-class DSL constructs.

## Phase 2: DSL Design

### Objective

Design a vendor-neutral language that lets users express infrastructure intent without choosing provider-specific resources too early.

### Method

Define:

- lexical syntax
- grammar
- type system
- resource declarations
- dependency declarations
- constraints
- policy annotations
- provider preference annotations
- degradation tolerance annotations

Example DSL direction:

```text
deploy web_app {
  compute api {
    instances: 3
    runtime: container
    autoscaling: enabled
  }

  database app_db {
    engine: postgres
    availability: managed
    backup: daily
  }

  storage assets {
    type: object
    public_read: false
  }
}
```

The DSL should avoid provider names in the core model. Provider-specific details may be allowed only through explicit extension blocks so that non-portable decisions are visible.

### Expected Output

- DSL grammar
- sample DSL programs
- static validation rules
- list of supported constructs
- explanation of intentionally unsupported features

### How This Answers RQ1

The DSL becomes the practical test of whether the selected concept set is expressive enough for common architectures.

## Phase 3: Semantic IR Design

### Objective

Develop an Intermediate Representation that captures infrastructure meaning, not only syntax.

### Method

Represent infrastructure as a typed semantic graph:

```text
Node: semantic resource
Edge: dependency, network reachability, binding, ownership, policy relation
Attribute: required behavior or constraint
Capability: provider feature needed to implement behavior
Diagnostic: warning, degradation, unsupported mapping, or alternative
```

The IR should include semantic categories such as:

- compute semantics: instance count, runtime, scaling, placement
- network semantics: public/private exposure, ingress/egress, routing, isolation
- storage semantics: durability, access model, encryption, lifecycle
- database semantics: engine, managed/self-managed, backups, high availability
- identity semantics: roles, permissions, trust boundaries
- lifecycle semantics: create/update/delete behavior, dependency order
- portability semantics: exact, partial, unsupported, provider extension

### Expected Output

- IR schema
- IR examples generated from DSL programs
- semantic validation rules
- invariants, such as "a private database must not be reachable from public ingress"

### How This Answers RQ2

RQ2 is answered by demonstrating that the IR can represent intent, relationships, constraints, and provider-independent semantics in a form suitable for backend lowering.

## Phase 4: Provider Capability Mapping

### Objective

Define how IR concepts map to backend/provider resources and how semantic differences are reported.

### Method

Create a capability profile for each backend/provider. A capability profile should describe whether a provider supports a semantic feature exactly, partially, through composition, or not at all.

Example mapping:

| IR concept | AWS target | Azure target | Kubernetes target | Mapping type |
| --- | --- | --- | --- | --- |
| ManagedPostgres | RDS PostgreSQL | Azure PostgreSQL Flexible Server | External operator or unsupported | Exact/partial |
| ObjectStorage | S3 | Blob Storage | MinIO or PVC-based alternative | Exact/partial |
| AutoscaledCompute | Auto Scaling Group/ECS | VMSS/Container Apps | Deployment + HPA | Partial |
| IdentityRole | IAM Role | Managed Identity/RBAC | ServiceAccount/RBAC | Partial |

Each mapping should include:

- target resource
- required parameters
- unsupported parameters
- semantic risks
- fallback alternatives
- fidelity score contribution

Mapping classification:

```text
exact: provider feature preserves required semantics
partial: provider feature preserves core behavior but changes some guarantees
composed: semantics can be achieved by combining multiple resources
degraded: approximate behavior with known loss
unsupported: no acceptable mapping
extension: provider-specific feature outside portable core
```

### Expected Output

- capability matrix
- mapping rules
- diagnostic rules
- examples of exact, partial, degraded, and unsupported mappings

### How This Answers RQ3

RQ3 is answered by showing a structured method for distinguishing exact equivalence from partial equivalence and unsupported provider features.

## Phase 5: Compiler Prototype

### Objective

Build a working prototype that compiles DSL specifications into one or more IaC backends.

### Compiler Pipeline

```text
DSL source
  -> lexer/parser
  -> AST
  -> type checker and semantic validator
  -> semantic IR
  -> provider capability resolver
  -> backend-specific lowering
  -> IaC output
  -> diagnostics and portability report
```

### Recommended Prototype Scope

Keep the first prototype focused but research-valid:

- DSL frontend
- JSON/YAML IR output
- Terraform/OpenTofu backend
- Kubernetes manifest backend
- optional CloudFormation or Bicep backend
- portability report generator

### Implementation Method

1. Define grammar and parser.
2. Generate AST from DSL examples.
3. Convert AST to IR.
4. Validate IR constraints.
5. Load provider capability profiles.
6. Resolve mappings.
7. Emit backend IaC files.
8. Emit diagnostics and scores.

### Expected Output

- compiler prototype
- generated IaC files
- generated IR files
- portability report
- test cases

### How This Answers RQ4

RQ4 is answered by compiling the same DSL inputs into multiple backend outputs and measuring whether the outputs preserve intended semantics.

## Phase 6: Evaluation Method

### Objective

Evaluate the compiler using portability, semantic fidelity, expressiveness, explainability, and backend validity.

### Benchmark Cases

Use a small benchmark suite of representative architectures:

| Case | Description | Concepts tested |
| --- | --- | --- |
| B1 | Static website with object storage | storage, public endpoint, access policy |
| B2 | Web app with managed PostgreSQL | compute, database, network, secrets |
| B3 | Autoscaled API service | compute, autoscaling, load balancing |
| B4 | Private service with internal database | network isolation, private routing, dependency |
| B5 | Serverless API with storage | function/runtime, event trigger, storage |
| B6 | Kubernetes-hosted app with external database | Kubernetes backend, managed service integration |

### Evaluation Metrics

#### 1. Portability Score

Measures how much of the DSL specification can be mapped across selected providers.

```text
Portability Score =
  weighted mapped features / total required semantic features
```

Suggested weights:

```text
exact mapping: 1.00
composed mapping: 0.85
partial mapping: 0.60
degraded mapping: 0.35
unsupported mapping: 0.00
```

#### 2. Semantic Fidelity Score

Measures how closely the generated backend preserves required behavior.

```text
Semantic Fidelity =
  preserved semantic constraints / total semantic constraints
```

Example constraints:

- database must remain private
- object storage must be encrypted
- compute must scale between min and max limits
- public endpoint must expose only declared ports
- backup policy must be preserved

#### 3. Expressiveness Score

Measures how many benchmark cases the DSL can represent without provider-specific escape hatches.

```text
Expressiveness =
  benchmark cases represented in portable DSL / total benchmark cases
```

#### 4. Backend Validity Score

Measures whether generated backend output passes tool validation.

Validation examples:

- `terraform validate`
- `tofu validate`
- `kubectl apply --dry-run=client`
- CloudFormation template validation, if implemented
- Bicep build/validation, if implemented

#### 5. Diagnostic Quality

Measures whether the compiler explains portability issues clearly.

A diagnostic should include:

- feature affected
- provider/backend affected
- reason
- severity
- semantic consequence
- suggested alternative

Example:

```text
Feature: database.backup.point_in_time_restore
Target: Kubernetes
Status: unsupported
Consequence: restore semantics cannot be guaranteed by native manifests
Suggested alternative: use managed database provider or database operator with PITR support
```

### Evidence Collection

Collect the following evidence for each benchmark:

- DSL source
- IR output
- generated backend files
- capability mapping report
- validation output
- portability score
- semantic fidelity score
- diagnostic report

### How This Answers RQ5

RQ5 is answered by defining and applying measurable criteria to the compiler outputs.

## Method for Answering Each Research Question

| Research question | Method | Evidence | Success criterion |
| --- | --- | --- | --- |
| RQ1 | Domain analysis and DSL construct selection | Concept taxonomy, DSL examples | Common architectures can be expressed without provider resources |
| RQ2 | IR design and semantic graph modeling | IR schema, IR examples, validation rules | IR captures resource intent, relationships, constraints, and lifecycle semantics |
| RQ3 | Provider capability profiling | Capability matrix, mapping rules, diagnostics | Exact, partial, degraded, and unsupported mappings are distinguishable |
| RQ4 | Compiler implementation and benchmark compilation | Generated IaC outputs, validation results | Same DSL source compiles to multiple backends with explainable differences |
| RQ5 | Metric definition and evaluation | Scores, tables, benchmark reports | Portability, fidelity, expressiveness, and diagnostics are measurable |

## Proposed Work Plan

### Phase 1: Literature and Requirement Consolidation

Duration: 2 weeks

Tasks:

- finalize literature review
- extract research gaps
- finalize research questions
- define thesis scope
- select target backends

Deliverables:

- literature summary
- finalized RQs
- initial requirement list

### Phase 2: Cloud Concept Taxonomy

Duration: 2 weeks

Tasks:

- study common resource models across Terraform, CloudFormation, Bicep, Kubernetes, TOSCA, CAMEL, DOML, and Crossplane
- classify concepts as portable, partial, provider-specific, or unsupported
- define first DSL construct set

Deliverables:

- concept taxonomy
- DSL feature list

### Phase 3: DSL Specification

Duration: 3 weeks

Tasks:

- design DSL syntax
- define type rules
- define resource and dependency semantics
- write example programs
- identify provider extension mechanism

Deliverables:

- DSL grammar
- sample DSL programs
- static validation rules

### Phase 4: IR and Capability Model

Duration: 3 weeks

Tasks:

- design semantic IR schema
- define graph node and edge types
- define semantic attributes
- define capability profile format
- create initial provider/backend mapping tables

Deliverables:

- IR schema
- example IR documents
- capability matrix

### Phase 5: Compiler Prototype

Duration: 5 weeks

Tasks:

- implement parser
- implement AST to IR conversion
- implement semantic validation
- implement capability resolver
- implement Terraform/OpenTofu backend
- implement Kubernetes backend
- generate diagnostics

Deliverables:

- working compiler prototype
- generated backend files
- portability report output

### Phase 6: Benchmark Suite and Evaluation

Duration: 4 weeks

Tasks:

- define benchmark architectures
- compile benchmarks to each backend
- validate generated backend output
- calculate portability and fidelity scores
- analyze unsupported/degraded features

Deliverables:

- benchmark suite
- evaluation tables
- analysis of results

### Phase 7: Thesis Writing and Refinement

Duration: 4 weeks

Tasks:

- write methodology chapter
- write design and implementation chapter
- write evaluation chapter
- discuss limitations
- refine diagrams, tables, and examples

Deliverables:

- thesis draft
- final diagrams
- final results

## Expected Contributions

The research is expected to contribute:

1. A vendor-neutral DSL for cloud infrastructure intent.
2. A semantic IR for infrastructure representation.
3. A provider capability mapping model.
4. A compiler prototype that lowers DSL specifications into IaC backends.
5. A portability and semantic fidelity evaluation framework.
6. A benchmark set for comparing generated infrastructure behavior across backends.

## Threats to Validity

### Scope limitation

The prototype may support only a subset of cloud services. This is acceptable if the subset is clearly justified and evaluated deeply.

### Provider evolution

Cloud providers change services frequently. Capability mappings should be versioned and treated as updateable data.

### Semantic equivalence difficulty

Some cloud features have no exact equivalent. The research should avoid claiming full equivalence where only approximation exists.

### Validation without live deployment

If live cloud deployment is not possible, validation may rely on static tool validation and generated resource graph inspection. This limitation should be stated clearly.

### Benchmark representativeness

The benchmark cases may not cover all real enterprise architectures. The selected cases should therefore cover different semantic categories rather than only one application style.

## Final Methodological Position

The thesis should not argue that cloud portability can be completely solved. A stronger and more defensible position is:

> Cloud portability can be made explicit, measurable, and explainable through a vendor-neutral DSL, semantic IR, provider capability mappings, and compiler diagnostics.

This position directly follows from the literature gap and gives the thesis a clear research identity: it is a compiler-based semantic portability study for cloud infrastructure.
