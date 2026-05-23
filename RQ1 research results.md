# RQ1 Research Results

## Research Question

RQ1: What minimal set of cloud infrastructure concepts can express common portable architectures across multiple providers without collapsing into provider-specific resources?

## Short Answer

The minimal portable concept set should not begin with provider resource names such as `aws_instance`, `AWS::RDS::DBInstance`, `azurerm_linux_virtual_machine`, or Kubernetes `Deployment`. It should begin with provider-independent infrastructure intent.

After a peer-reviewed literature review focused only on RQ1, the answer is refined as follows:

> A vendor-neutral cloud architecture definition language requires a semantic portability kernel consisting of deployment unit, deployment interface, workload/component, runtime and artifact model, capacity profile, network boundary, endpoint, storage, data service, identity principal, access policy, configuration/secrets, scaling/adaptation policy, placement/availability, requirements/SLO constraints, and relationship/lifecycle semantics. These concepts are sufficient to describe the selected common portable architectures while keeping provider-specific resource details outside the core language.

This is a refinement of the earlier RQ1 result. The earlier answer correctly identified compute, network, storage, database, security, scaling, placement, and dependency concepts. The peer-reviewed literature shows that three concepts must be made more explicit:

- deployment interface: inputs, outputs, and externally supplied values
- runtime/artifact/capacity: what runs, how it runs, and with what resource profile
- requirements/SLO constraints: performance, cost, security, placement, scalability, and availability requirements that guide provider-independent modelling and backend selection

## Method Used to Answer RQ1

The answer was derived using two evidence layers.

First, local thesis sources were analyzed:

- `Synopsis.md`: defines the objective of a vendor-agnostic DSL, semantic IR, compiler pipeline, provider mappings, and evaluation through portability, semantic fidelity, and expressiveness.
- `Outcomes.md`: identifies the expected outcome as a cloud portability engine, semantic IR, portability analyzer, and multi-cloud compiler.
- `Existing literature.md`: compares provider-native IaC, multi-provider IaC, TOSCA, CAMEL, DOML, Crossplane, OAM, and Infrastructure-from-Code approaches.
- `work plan and methodology.md`: defines domain analysis as the method for answering RQ1 and benchmark architectures as the evidence base.

Second, a focused literature review was conducted using peer-reviewed publications only. Non-peer-reviewed standards pages and vendor documentation were not used as evidence for this revision. The reviewed literature includes peer-reviewed journal papers, conference papers, and scholarly book chapters on cloud modelling languages, model-driven multi-cloud deployment, TOSCA-based orchestration, SLO modelling, secure multi-cloud deployment, and multi-cloud native architecture.

The concept set was selected using five criteria:

1. Cross-provider recurrence: the concept appears across multiple clouds, IaC systems, or cloud modelling approaches.
2. Architectural necessity: the concept is required to describe common infrastructure patterns.
3. Semantic usefulness: the concept carries behavior that must be preserved or diagnosed during translation.
4. Provider neutrality: the concept can be described without naming a provider-specific service.
5. Lowering feasibility: the concept can be mapped into at least two realistic backend families, even if the mapping is partial.

## Peer-Reviewed Literature Review for RQ1

### TOSCA and topology-oriented cloud modelling

Binz et al. present TOSCA as a way to support portable automated deployment and management of cloud applications. The important learning for RQ1 is that a portable cloud language must represent application components, their relationships, deployment artifacts, and lifecycle management, not just individual infrastructure resources [1].

Brogi et al. further show that TOSCA application descriptions can be matched by considering behaviour, exact compatibility, and relaxed/plug-in compatibility [2]. This is important for RQ1 because the DSL should preserve enough semantic detail to later support RQ2 and RQ3: exact matches, partial matches, and degraded alternatives.

TORCH, a TOSCA-based orchestrator for multi-cloud containerized applications, reinforces the need to separate provider-neutral application requirements from proprietary cloud API invocation [3]. This supports keeping provider-specific resources outside the DSL core.

TOSCAdata extends TOSCA for data-pipeline applications and highlights that generic application topology is not enough for data-intensive systems; data movement, processing, scheduling, reuse, and multi-cloud data flow may need explicit modelling [4]. This supports generalizing `database` into `data service`, with future extension toward data flow and data lifecycle.

### CloudML, CloudMF, and model-driven multi-cloud management

Ferry et al. argue that multi-cloud systems need model-driven techniques for specifying provisioning, deployment, monitoring, and adaptation concerns at design time and enacting them at runtime [5]. This supports three RQ1 concepts: deployment unit, lifecycle semantics, and scaling/adaptation policy.

CloudMF provides a DSL for provisioning and deploying multi-cloud applications, together with a models@runtime environment for continuous provisioning, deployment, and adaptation [6]. The RQ1 implication is that a portable DSL should not be limited to a static resource list. It should include enough relationship, lifecycle, and adaptation semantics to support later runtime-aware analysis.

CAMEL is especially important because it integrates multiple domain-specific languages for multi-cloud application management. It covers provisioning, deployment, requirements, scalability, security, and runtime adaptation [7]. In CAMEL, users define provider-independent topology, application requirements such as SLOs and optimization goals, and scalability rules. This strongly supports adding `requirements/SLO constraints` as an explicit concept in the RQ1 result.

DOML, the DevOps Modeling Language, provides a cloud modelling language that can be mapped into multiple IaC languages and separates application deployment/configuration from infrastructure provisioning [8]. This supports the separation between workload/component, runtime environment, abstract infrastructure, and concrete provider-specific lowering.

### Multi-cloud architecture and resource characterization

Alonso et al.'s systematic literature review of multi-cloud native applications identifies open research needs around stateful/stateless component design, resource classification, non-functional requirements, network elements, security, cost, latency, performance, and cloud-agnostic design [9]. This is a strong peer-reviewed justification for including not only resources, but also placement, network, state, data service, security, and requirements/SLO constraints in the minimal concept set.

Quint and Kratzke propose a lightweight multi-cloud DSL for elastic and transferable cloud-native applications. Their work is useful because it deliberately reduces language scope and focuses on containerized deployments, services, ports, CPU/memory requests, scaling rules, scheduling constraints, and separation between elastic platform definition and application definition [10]. This supports the RQ1 decision to keep the core small but to include runtime/artifact, endpoint, capacity, scaling, and placement/constraint concepts.

### Requirements, SLOs, and security constraints

SLO-ML argues that many cloud modelling languages lack practical support for selecting services based on application service-level objectives, and proposes a generative language for capturing SLO requirements and producing deployment code [11]. This directly supports adding `requirements/SLO constraints` to the RQ1 concept set.

The MUSA deployer work shows that secure multi-cloud deployment requires application security requirements and Security Service Level Agreements to be expressed and evaluated before automated deployment [12]. This supports access policy and security constraints as portable intent, even though the backend implementations will differ.

### Cloud-agnostic adaptation and portability

Miranda et al. propose a model-driven approach to adaptive multi-cloud applications and emphasize that vendor-specific technologies and services cause migration and portability problems during the design stage [13]. Their work supports the RQ1 rule that provider-specific features should not enter the portable core; instead, the DSL should express semantic intent and expose non-portable decisions explicitly.

## Critical Learnings from Peer-Reviewed Literature

The peer-reviewed literature leads to six changes or confirmations.

### 1. RQ1 is not only a resource taxonomy problem

The concept set should not be a list of cloud resources. TOSCA, CloudMF, CAMEL, DOML, and SLO-ML all show that portable cloud modelling requires components, relationships, requirements, lifecycle, constraints, and deployment concerns. Therefore, the DSL core must represent both resources and semantic relations.

### 2. Requirements and SLOs must be explicit

The strongest correction to the earlier result is the addition of `requirements/SLO constraints`. CAMEL, SLO-ML, MUSA, and the multi-cloud SLR all show that cost, performance, latency, security, availability, location, and scalability requirements are essential to cloud portability. These constraints guide provider selection and later capability mapping.

### 3. Application components and infrastructure must be separated

DOML, CloudMF, and the lightweight multi-cloud DSL all support separating application-level components from infrastructure or platform-level implementation. This reinforces the thesis direction: DSL source should express workload intent, while IR and backend lowering handle provider-specific resources.

### 4. Runtime, artifacts, and capacity cannot be hidden

The lightweight DSL and TOSCA-related work show that a deployable cloud application needs executable artifacts, runtime assumptions, resource requests, ports/endpoints, scaling rules, and scheduling/placement constraints. These should be first-class semantic attributes even if they are nested under workload.

### 5. Data must be treated beyond a simple database label

TOSCAdata shows that data-intensive cloud applications require modelling data flow, data migration, processing, scheduling, and storage. For this thesis prototype, `data service` can begin with relational database semantics, but the core should be extensible toward data flow and data lifecycle.

### 6. Security is a portable intent but not a portable implementation

MUSA and CAMEL show that security requirements are central in multi-cloud modelling. However, IAM, RBAC, managed identity, service accounts, and policy systems differ significantly. Therefore, RQ1 should include identity and access policy as intent-level concepts, while RQ3 must classify their backend mappings as exact, partial, degraded, or unsupported.

## Revised Minimal Concept Set

| No. | Core concept | Purpose in the DSL | Peer-reviewed support | Example provider lowering |
| --- | --- | --- | --- | --- |
| 1 | Deployment unit | Groups related infrastructure into one compilation and deployment boundary | TOSCA, CloudMF, DOML | Terraform module, CloudFormation stack, Bicep deployment, Kubernetes namespace/manifests |
| 2 | Deployment interface | Represents inputs, outputs, external values, and exported connection details | TOSCA service templates, DOML | Terraform variables/outputs, CloudFormation Parameters/Outputs, Bicep parameters/outputs |
| 3 | Workload/component | Represents executable application or service component intent | TOSCA, CloudMF, DOML, multi-cloud SLR | VM, container, function, job, service |
| 4 | Runtime and artifact model | Describes how the workload runs and what artifact is executed | TOSCA artifacts, lightweight multi-cloud DSL | VM image, container image, function package, startup script |
| 5 | Capacity profile | Represents CPU, memory, storage size, performance class, or abstract size | Lightweight multi-cloud DSL, SLO-ML, multi-cloud SLR | Instance type, VM size, Kubernetes requests/limits, database tier |
| 6 | Network boundary | Represents isolation, private/public zones, routing scope, and network constraints | Multi-cloud SLR, TOSCA/TORCH | VPC/VNet, subnet, security group, NetworkPolicy |
| 7 | Endpoint | Represents exposed access to a workload or service | Lightweight multi-cloud DSL, TOSCA, DOML | Load balancer, API gateway, Kubernetes Service/Ingress |
| 8 | Storage | Represents persistent data storage | TOSCAdata, CloudMF, DOML | Object storage, block volume, file share, persistent volume |
| 9 | Data service | Represents managed stateful services, with database as first subtype | TOSCAdata, multi-cloud SLR | Managed PostgreSQL, cache, queue, stream, data pipeline component |
| 10 | Identity principal | Represents who or what can act | CAMEL security modelling, MUSA | IAM role, managed identity, service account |
| 11 | Access policy | Represents allowed actions over resources | CAMEL, MUSA | IAM policy, Azure RBAC assignment, Kubernetes RBAC |
| 12 | Configuration and secrets | Represents runtime configuration and sensitive values | DOML, TOSCA artifacts/properties, MUSA | Secret manager, parameter store, ConfigMap, Secret |
| 13 | Scaling/adaptation policy | Represents elasticity and runtime adaptation intent | CAMEL scalability rules, CloudMF models@runtime, lightweight DSL | Autoscaling group, HPA, VMSS autoscale |
| 14 | Placement and availability | Represents region, zone, replication, scheduling, and fault-domain requirements | CAMEL, SLO-ML, multi-cloud SLR | Region/AZ, node affinity, zone redundancy |
| 15 | Requirements/SLO constraints | Represents cost, latency, performance, security, availability, location, and compliance constraints | CAMEL, SLO-ML, MUSA, multi-cloud SLR | Capability selection rules, policy checks, deployment diagnostics |
| 16 | Relationship and lifecycle semantics | Represents dependency, binding, ownership, requirements/capabilities, creation order, and lifecycle operations | TOSCA, CloudMF, DOML | Dependency graph, DependsOn, owner refs, orchestration workflow |

## Concept Categories

The revised concepts can be grouped into six semantic layers.

### 1. Deployment Structure Layer

These concepts define the boundary and reusable interface of the infrastructure:

- deployment unit
- deployment interface
- relationship and lifecycle semantics

This layer is supported by TOSCA-style topology modelling and DOML's approach of mapping abstract deployment models into IaC backends.

### 2. Execution Layer

These concepts define application execution:

- workload/component
- runtime and artifact model
- capacity profile
- scaling/adaptation policy

This layer captures the difference between "run this service" and provider-specific resources such as EC2, ECS, Lambda, Azure Functions, or Kubernetes Deployment.

### 3. Connectivity Layer

These concepts define reachability and exposure:

- network boundary
- endpoint

The multi-cloud SLR identifies network elements and heterogeneous infrastructure characterization as open issues in multi-cloud design. Therefore, networking must be explicit in the portable core.

### 4. Data Layer

These concepts define persistence, state, and managed data capability:

- storage
- data service

Storage and data services are separated because object storage, block storage, file storage, managed relational databases, queues, caches, streams, and data pipelines have different durability, consistency, scheduling, lifecycle, and portability properties.

### 5. Security and Configuration Layer

These concepts define trust, access, and runtime parameters:

- identity principal
- access policy
- configuration and secrets

Security should be modelled as portable intent, but its backend lowering must be treated carefully because cloud identity and policy systems differ significantly.

### 6. Requirements and Resilience Layer

These concepts define quality goals and constraints:

- requirements/SLO constraints
- placement and availability
- scaling/adaptation policy
- capacity profile

This layer is the most important addition from the peer-reviewed review. Requirements are not merely annotations; they drive resource selection, portability analysis, and diagnostic reporting.

## Why These Concepts Are Minimal

The refined concept set is minimal in a research sense because removing any concept weakens expressiveness, portability analysis, or backend lowering:

- Without deployment unit, the infrastructure has no unit of compilation, validation, or output.
- Without deployment interface, the DSL cannot represent reusable inputs, outputs, and environment-specific values.
- Without workload/component, the language cannot describe executable application structure.
- Without runtime and artifact model, it cannot distinguish VM, container, function, job, image, package, and script semantics.
- Without capacity profile, provider lowerers must guess instance size, resource limits, storage size, or service tier.
- Without network boundary, it cannot describe private services, isolation, routing, or network constraints.
- Without endpoint, it cannot describe externally reachable APIs, internal services, or load-balanced access.
- Without storage, it cannot describe object assets, volumes, filesystems, or persistent data.
- Without data service, it cannot model managed PostgreSQL and similar stateful services.
- Without identity principal and access policy, it cannot represent security intent independently of IAM/RBAC implementations.
- Without configuration and secrets, it cannot model real deployable workloads.
- Without scaling/adaptation policy, it cannot represent elasticity and runtime adaptation.
- Without placement and availability, it cannot reason about regions, zones, redundancy, scheduling, or disaster tolerance.
- Without requirements/SLO constraints, it cannot express why one provider mapping is acceptable while another is not.
- Without relationship/lifecycle semantics, it cannot construct a semantic IR or compile reliably to IaC backends.

The set is intentionally not a complete catalog of all cloud services. It is a portability kernel: the smallest practical set of concepts needed to express common architectures and feed the thesis IR, capability mapping, and compiler pipeline.

## Concepts That Should Not Be in the Portable Core

Some concepts should not be first-class portable core constructs because they are provider-specific, tool-specific, or too semantically unstable across clouds.

| Excluded from portable core | Reason | Recommended handling |
| --- | --- | --- |
| AWS Lambda Layers | Strongly AWS-specific packaging concept | Provider extension with portability warning |
| IAM condition keys | Provider-specific policy semantics | Provider extension with degraded/unsupported mapping |
| ALB sticky sessions | Load balancer behavior differs across providers | Endpoint extension with diagnostic |
| Azure-specific resource groups | Azure organizational and deployment construct | Backend target concern |
| CloudFormation transforms/macros | IaC-engine-specific feature | Backend-only feature |
| Terraform provisioners | Tool-specific lifecycle escape hatch | Excluded or marked non-portable |
| Kubernetes CRDs for specific operators | Runtime-specific abstraction | Extension or backend-specific target |
| Proprietary AI/data services | Semantics differ strongly by provider | Extension with no assumed portability |
| Detailed observability dashboards | Important operational feature, but not required for minimal provisioning semantics | Future extension |
| Cost and billing controls | Important governance feature, but provider billing models differ substantially | Model as high-level requirement, not provider-specific billing logic |

This supports the thesis novelty from `Outcomes.md`: the project should not claim that every cloud feature is portable. It should identify non-portable features before deployment and explain their consequences.

## Initial DSL Construct Proposal

The refined concept set can be expressed through the following initial DSL construct families:

```text
deploy <name> {
  input <name> { ... }
  output <name> { ... }

  requirement <name> { ... }

  workload <name> { ... }
  network <name> { ... }
  endpoint <name> { ... }
  storage <name> { ... }
  data_service <name> { ... }
  identity <name> { ... }
  policy <name> { ... }
  secret <name> { ... }
  config <name> { ... }
  placement <name> { ... }

  relation <name> { ... }
}
```

Not every concept needs to appear as a top-level block in every program. Some concepts, such as capacity, runtime, artifact, and scaling, may be nested inside `workload`, `storage`, or `data_service` blocks.

Example:

```text
deploy web_app {
  input environment {
    type: string
    default: "dev"
  }

  requirement production_slo {
    availability: "99.9%"
    max_latency_ms: 250
    data_residency: "IN"
    cost_class: balanced
  }

  placement default {
    region: any
    availability: multi_zone_preferred
  }

  network private_net {
    exposure: private
  }

  workload api {
    runtime: container
    artifact: {
      image: "app:latest"
    }
    capacity: {
      cpu: 2
      memory: "4Gi"
    }
    instances: 3
    network: private_net
    scale: {
      min: 2
      max: 6
      metric: cpu
    }
  }

  data_service app_db {
    kind: relational_database
    engine: postgres
    mode: managed
    network: private_net
    capacity: {
      class: general_purpose
      storage_size: "100Gi"
    }
    backup: daily
  }

  endpoint public_api {
    target: api
    protocol: https
    exposure: public
  }

  secret db_password {
    scope: app_db
  }

  policy api_to_db {
    subject: api
    action: connect
    resource: app_db
  }

  output api_url {
    value: public_api.url
  }
}
```

This example avoids provider resources while still carrying enough semantics for provider-specific lowering.

## Coverage Against Benchmark Architectures

The work plan proposed six benchmark cases. The revised concept set covers them as follows:

| Benchmark case | Required concepts | Covered by revised set |
| --- | --- | --- |
| B1: Static website with object storage | deployment unit/interface, storage, endpoint, access policy, artifact | Yes |
| B2: Web app with managed PostgreSQL | workload, runtime/artifact, data service, network, secrets, policy, capacity, SLOs | Yes |
| B3: Autoscaled API service | workload, endpoint, scaling/adaptation, network, capacity, performance requirement | Yes |
| B4: Private service with internal database | workload, data service, network boundary, relation, policy, placement | Yes |
| B5: Serverless API with storage | workload, runtime model, artifact, endpoint, storage, policy | Yes, if runtime includes `function` |
| B6: Kubernetes-hosted app with external database | workload, runtime/artifact, data service, endpoint, network, configuration, identity | Yes |

This gives a preliminary expressiveness result:

```text
Benchmark coverage: 6 / 6 representative cases
Preliminary expressiveness: 100 percent for selected benchmark scope
```

This result should be interpreted cautiously. It means the concept set can describe the selected benchmark architectures at the DSL level. It does not prove that every backend can preserve all semantics exactly. Backend-specific semantic fidelity must be evaluated under RQ4 and RQ5.

## Critical Analysis of the Revised Answer

The peer-reviewed literature strengthens the RQ1 answer but also makes it more nuanced.

The concept set is stronger than the earlier version because it separates concerns that were previously hidden:

- Deployment interface is separated from deployment unit.
- Runtime and artifact are separated from generic workload.
- Capacity profile is separated from scaling and placement.
- Requirements/SLO constraints are separated from individual resources.
- Data service is generalized beyond relational database alone.

This makes the DSL more suitable for compiler design. A compiler needs explicit inputs, outputs, artifacts, capacity constraints, requirements, and relationships in order to produce backend-specific IaC predictably.

The tradeoff is that the set is larger. A strict interpretation of "minimal" might prefer fewer constructs. However, peer-reviewed cloud modelling work shows that reducing the core too far creates hidden assumptions. For example, if SLOs are not explicit, provider selection becomes arbitrary. If capacity is not explicit, generated instance sizes become arbitrary. If artifacts are not explicit, workload lowering becomes ambiguous.

The most uncertain concepts are:

- data service: useful for managed PostgreSQL and future data services, but difficult to generalize across all PaaS offerings.
- identity and access policy: portable at the intent level, but often only partially portable at the implementation level.
- placement and availability: necessary for resilience, but provider availability-zone semantics and managed-service guarantees differ.
- capacity profile: necessary for backend generation, but mappings from abstract capacity to provider SKUs may be approximate.
- requirements/SLO constraints: essential for semantic portability, but difficult to evaluate without provider capability and monitoring data.

These uncertainties do not invalidate the RQ1 result. They identify the exact areas where RQ2 and RQ3 must be careful. The IR must preserve these concepts as semantic intent, and provider mappings must classify results as exact, partial, degraded, unsupported, or provider-specific.

## Mapping to Thesis Outcomes

The answer to RQ1 directly supports the expected thesis outcomes.

| Thesis outcome | RQ1 contribution |
| --- | --- |
| Vendor-neutral cloud compiler | Defines the portable source concepts accepted by the compiler |
| Semantic IR | Provides the semantic categories that the IR must represent |
| Cloud portability analysis engine | Separates portable, partial, provider-sensitive, and non-portable features |
| Multi-backend IaC generation | Gives backend lowerers a stable provider-neutral input |
| Benchmark suite | Supplies the concept coverage needed by representative architectures |

## Preliminary Concept Taxonomy

| Category | Concepts | Portability status |
| --- | --- | --- |
| Portable core | deployment unit, deployment interface, workload/component, endpoint, relationship/lifecycle | High |
| Portable with semantic variation | runtime/artifact, capacity, storage, data service, scaling/adaptation, placement/availability, requirements/SLO constraints | Medium |
| Portable but provider-sensitive | identity principal, access policy, secrets, security requirements | Medium to low |
| Non-portable extension | provider-specific IAM conditions, load balancer features, proprietary managed services, engine-specific macros | Low |

This taxonomy should become an input to RQ2, where the semantic IR will represent these concepts as nodes, edges, attributes, constraints, and portability annotations.

## Research Finding

The first research question can be answered with the following finding:

> The minimal concept set for a vendor-neutral cloud architecture DSL consists of deployment unit, deployment interface, workload/component, runtime and artifact model, capacity profile, network boundary, endpoint, storage, data service, identity principal, access policy, configuration/secrets, scaling/adaptation policy, placement/availability, requirements/SLO constraints, and relationship/lifecycle semantics. These concepts are sufficient to express the selected benchmark architectures without using provider-specific resource names, while preserving enough semantic information for IR construction, provider capability mapping, backend lowering, and portability diagnostics.

## Implication for the Next Research Question

RQ1 identifies what the DSL must express. RQ2 must now define how these concepts are represented internally.

The next step is to transform the concept set into a semantic IR with:

- typed nodes for each infrastructure concept
- typed edges for dependency, reachability, binding, ownership, policy, requirement, and capability relations
- attributes for required behavior
- constraints for validation
- inputs and outputs for the deployment interface
- artifact references for runnable workloads
- SLO/requirement records for performance, cost, availability, security, location, and compliance goals
- portability annotations for exact, partial, degraded, unsupported, and provider-extension mappings

This connects the RQ1 answer directly to the planned IR contribution of the thesis.

## References

[1] Binz, T., Breitenbuecher, U., Kopp, O., and Leymann, F., "TOSCA: Portable Automated Deployment and Management of Cloud Applications," in Advanced Web Services, Springer, 2014, pp. 527-549. https://doi.org/10.1007/978-1-4614-7535-4_22

[2] Brogi, A., Canciani, A., and Soldani, J., "Simulation-based matching of cloud applications," Science of Computer Programming, 2017. https://www.sciencedirect.com/science/article/pii/S0167642317301223

[3] Tomarchio, O., Calcaterra, D., Di Modica, G., and Mazzaglia, P., "TORCH: a TOSCA-Based Orchestrator of Multi-Cloud Containerised Applications," Journal of Grid Computing, 2021. https://link.springer.com/article/10.1007/s10723-021-09549-z

[4] Dehury, C. K., Jakovits, P., Srirama, S. N., Giotis, G., and Garg, G., "TOSCAdata: Modeling data pipeline applications in TOSCA," Journal of Systems and Software, 2022. https://www.sciencedirect.com/science/article/pii/S0164121221002508

[5] Ferry, N., Rossini, A., Chauvel, F., Morin, B., and Solberg, A., "Towards model-driven provisioning, deployment, monitoring, and adaptation of multi-cloud systems," IEEE CLOUD, 2013. https://doi.org/10.1109/CLOUD.2013.133

[6] Ferry, N., Chauvel, F., Song, H., Rossini, A., Lushpenko, M., and Solberg, A., "CloudMF: Model-Driven Management of Multi-Cloud Applications," ACM Transactions on Internet Technology, 2018. https://doi.org/10.1145/3125621

[7] Achilleos, A. P. et al., "The cloud application modelling and execution language," Journal of Cloud Computing, 2019. https://link.springer.com/article/10.1186/s13677-019-0138-7

[8] Chiari, M. et al., "DOML: A new modeling approach to Infrastructure-as-Code," Information Systems, 2024. https://www.sciencedirect.com/science/article/pii/S0306437924000802

[9] Alonso, J. et al., "Understanding the challenges and novel architectural models of multi-cloud native applications: a systematic literature review," Journal of Cloud Computing, 2023. https://link.springer.com/article/10.1186/s13677-022-00367-6

[10] Quint, P.-C., and Kratzke, N., "Towards a Lightweight Multi-Cloud DSL for Elastic and Transferable Cloud-native Applications," CLOSER, 2018. https://www.scitepress.org/Papers/2018/66838/

[11] Elhabbash, A., Jumagaliyev, A., Blair, G. S., and Elkhatib, Y., "SLO-ML: A Language for Service Level Objective Modelling in Multi-cloud Applications," IEEE/ACM UCC, 2019. https://doi.org/10.1145/3344341.3368805

[12] Casola, V., De Benedictis, A., Rak, M., Villano, U., Rios, E., Rego, A., and Capone, G., "Model-based deployment of secure multi-cloud applications," International Journal of Grid and Utility Computing, 2019. https://www.inderscience.com/info/inarticle.php?artid=102710

[13] Miranda, J., Guillen, J., Murillo, J. M., and Canal, C., "Development of Adaptive Multi-cloud Applications: A Model-Driven Approach," MODELSWARD, 2013. https://www.scitepress.org/papers/2013/43706/43706.pdf
