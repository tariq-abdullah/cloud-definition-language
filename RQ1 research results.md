# RQ1 Research Results

## Research Question

RQ1: What minimal set of cloud infrastructure concepts can express common portable architectures across multiple providers without collapsing into provider-specific resources?

## Short Answer

The minimal portable concept set should not begin with provider resource names such as `aws_instance`, `AWS::RDS::DBInstance`, `azurerm_linux_virtual_machine`, or Kubernetes `Deployment`. It should begin with provider-independent infrastructure intent.

After an independent literature reassessment, the RQ1 answer is refined as follows:

> A vendor-neutral cloud architecture definition language requires a small semantic core consisting of deployment unit and interface, workload, runtime and artifact model, capacity profile, network boundary, endpoint, storage, data service, identity principal, access policy, configuration/secrets, scaling policy, placement/availability, and relationship/lifecycle semantics. These concepts are sufficient to describe common portable architectures while keeping provider-specific details outside the language core.

The revised set is slightly broader than the first RQ1 draft. The earlier draft correctly identified compute, network, storage, database, security, scaling, placement, and dependency concepts. However, independent literature shows that a reusable cloud definition also needs: deployment inputs/outputs, an artifact/package reference for runnable workloads, and a provider-neutral way to express capacity without directly naming provider instance types or SKUs.

## Method Used to Answer RQ1

The answer was derived using two evidence layers.

First, local thesis sources were analyzed:

- `Synopsis.md`: defines the objective of a vendor-agnostic DSL, semantic IR, compiler pipeline, provider mappings, and evaluation through portability, semantic fidelity, and expressiveness.
- `Outcomes.md`: identifies the expected outcome as a cloud portability engine, semantic IR, portability analyzer, and multi-cloud compiler.
- `Existing literature.md`: compares provider-native IaC, multi-provider IaC, TOSCA, CAMEL, DOML, Crossplane, OAM, and Infrastructure-from-Code approaches.
- `work plan and methodology.md`: defines domain analysis as the method for answering RQ1 and benchmark architectures as the evidence base.

Second, an independent literature search was performed across standards, official documentation, and related research:

- NIST cloud definition and cloud reference architecture [1], [2].
- TOSCA v2.0 service topology model [3].
- OCCI/TOSCA model-based cloud resource management research [4].
- Terraform, CloudFormation, Azure ARM/Bicep, OpenStack Heat, and Kubernetes documentation [5], [6], [7], [8], [9], [10].
- SPIFFE workload identity documentation [11].

The concept set was selected using five criteria:

1. Cross-provider recurrence: the concept appears across multiple clouds, IaC systems, or standards.
2. Architectural necessity: the concept is required to describe common infrastructure patterns.
3. Semantic usefulness: the concept carries behavior that must be preserved or diagnosed during translation.
4. Provider neutrality: the concept can be described without naming a provider-specific service.
5. Lowering feasibility: the concept can be mapped into at least two realistic backend families, even if the mapping is partial.

## Independent Literature-Based Reassessment

The original RQ1 result was largely valid, but the independent literature study suggests four refinements.

### 1. Deployment interface should be explicit

CloudFormation, Azure ARM templates, OpenStack Heat, and TOSCA all distinguish the deployable unit from its inputs and outputs. CloudFormation has Resources, Parameters, and Outputs [6]. ARM templates have parameters, variables, resources, and outputs [7]. Heat templates describe resources, parameters, inputs, constraints, and dependencies [8]. TOSCA service templates also include inputs, outputs, node templates, relationship templates, policies, workflows, and substitution mappings [3].

Therefore, the DSL should not only have a `deploy` block. It should also model a deployment interface:

- input parameters
- output values
- environment-specific values
- externally supplied resources
- exported connection details

This matters because portability is not only about resource translation. It is also about reusing the same architecture across environments without rewriting the program.

### 2. Workload artifact/package should be part of the workload model

The first draft included workload and runtime model but treated runnable artifacts only as an example attribute. TOSCA explicitly models artifacts, and Kubernetes workload definitions normally identify runnable workload resources and containerized application execution [3], [9]. Serverless and VM-based deployments also require some executable package, image, script, or machine image.

Therefore, `artifact` should be represented as a first-class workload sub-concept. It does not need to be a top-level block in every DSL program, but the language must be able to represent what is being run.

Examples:

- container image
- VM image
- function package
- initialization script
- deployment archive

### 3. Capacity profile should be separated from provider-specific size names

The earlier draft included workload and scaling but did not explicitly represent capacity. In real IaC systems, users must specify instance size, SKU, CPU, memory, disk, storage capacity, resource limits, or database tier through resource arguments and properties [5], [6], [7], [9]. If the DSL omits capacity, backend generation becomes arbitrary. If it uses provider names such as `t3.micro` or `Standard_B2s`, the language collapses into provider-specific syntax.

Therefore, the DSL needs a provider-neutral capacity profile.

Examples:

```text
capacity: small
cpu: 2
memory: 4Gi
storage_size: 100Gi
performance_class: general_purpose
```

Provider lowerers can then map these to instance types, SKUs, Kubernetes resource requests, or database tiers.

### 4. Database should be generalized as a data service

The earlier draft treated database as a core concept. That is justified for the selected benchmark cases, especially managed PostgreSQL. However, cloud definitions and model-based cloud research describe broader pools of configurable resources and heterogeneous services, including compute, storage, networks, applications, and services [1], [4]. In practice, providers expose many managed data services: databases, caches, queues, streams, object stores, and analytics services.

To avoid creating a new top-level DSL construct for every provider service, the refined concept is `data service`, with `database` as a required first subtype for the prototype.

Example:

```text
data_service app_db {
  kind: relational_database
  engine: postgres
  mode: managed
}
```

This preserves the thesis focus while leaving space for future extensions such as queue, cache, or stream.

## Revised Minimal Concept Set

| No. | Core concept | Purpose in the DSL | Why it is needed | Example provider lowering |
| --- | --- | --- | --- | --- |
| 1 | Deployment unit | Groups related infrastructure into one compilation and deployment boundary | Every architecture needs a scope for resources, dependencies, validation, and backend output | Terraform module, CloudFormation stack, ARM/Bicep deployment, Heat stack, Kubernetes namespace/manifests |
| 2 | Deployment interface | Represents inputs, outputs, externally supplied values, and exported connection details | Reusable infrastructure definitions need parameters and outputs across environments | Terraform variables/outputs, CloudFormation Parameters/Outputs, ARM parameters/outputs, TOSCA inputs/outputs |
| 3 | Workload | Represents executable compute intent | Required for web apps, APIs, workers, jobs, and services | EC2/ECS/Lambda, Azure VM/Container Apps/Functions, Kubernetes Deployment/Job |
| 4 | Runtime and artifact model | Describes how the workload runs and what artifact is executed | VM, container, function, and job deployments require different lowering rules and artifact handling | AMI/image, container image, function package, cloud-init/script |
| 5 | Capacity profile | Represents CPU, memory, storage size, performance class, or abstract size | Capacity must be portable without provider-specific instance/SKU names | EC2 instance type, Azure VM size, Kubernetes requests/limits, database tier |
| 6 | Network boundary | Represents isolation, private/public zones, and routing scope | Network semantics are central to security and portability | VPC/VNet, subnet, security group, Kubernetes NetworkPolicy |
| 7 | Endpoint | Represents exposed access to a workload or service | Required for web apps, APIs, load balancers, and internal services | ALB/API Gateway, Azure Load Balancer/App Gateway, Kubernetes Service/Ingress |
| 8 | Storage | Represents persistent data storage | Common architectures need object, block, or file storage | S3/EBS/EFS, Azure Blob/Disk/Files, PVC/MinIO |
| 9 | Data service | Represents managed stateful services, with database as the first required subtype | Managed services differ across providers and need semantic mapping | RDS PostgreSQL, Azure PostgreSQL, Cloud SQL, database operator |
| 10 | Identity principal | Represents who or what can act | IAM and RBAC are provider-specific, but identity intent can be modeled independently of provider accounts; SPIFFE is one example of platform-neutral workload identity [11] | IAM role, managed identity, service account, SPIFFE ID |
| 11 | Access policy | Represents allowed actions over resources | Security intent must be preserved and diagnosed | IAM policy, Azure RBAC role assignment, Kubernetes RBAC |
| 12 | Configuration and secrets | Represents runtime configuration and sensitive values | Real workloads need environment variables, credentials, tokens, and connection strings | Secrets Manager, Parameter Store, Key Vault, Kubernetes Secret/ConfigMap |
| 13 | Scaling policy | Represents elasticity intent | Elasticity is a cloud-defining property and implemented differently across providers | Auto Scaling Group, VMSS autoscale, Kubernetes HPA |
| 14 | Placement and availability | Represents region, zone, replication, and fault-domain requirements | Portability depends on location, redundancy, and availability behavior | AWS region/AZ, Azure region/zones, Kubernetes node/zone affinity |
| 15 | Relationship and lifecycle semantics | Represents dependency, binding, ownership, requirement/capability, creation order, and deletion behavior | A compiler requires a semantic graph, not just a flat resource list | Terraform dependency graph, CloudFormation DependsOn, TOSCA relationships, Kubernetes owner/reference relations |

## Concept Categories

The revised concepts can be grouped into six semantic layers.

### 1. Deployment Structure Layer

These concepts define the boundary and reusable interface of the infrastructure:

- deployment unit
- deployment interface
- relationship and lifecycle semantics

This layer is strongly supported by TOSCA, CloudFormation, ARM templates, Terraform, and Heat, all of which distinguish the deployable definition from resources, parameters, outputs, and relationships.

### 2. Execution Layer

These concepts define application execution:

- workload
- runtime and artifact model
- capacity profile
- scaling policy

This layer captures the difference between "run this service" and provider-specific resources such as EC2, ECS, Lambda, Azure Functions, or Kubernetes Deployment.

### 3. Connectivity Layer

These concepts define reachability and exposure:

- network boundary
- endpoint

Many portability failures occur in networking because public/private access, ingress rules, subnet behavior, load balancer features, service discovery, and network policy differ substantially across platforms.

### 4. Data Layer

These concepts define persistence and state:

- storage
- data service

Storage and data services are separated because object storage, block storage, file storage, managed relational databases, queues, and caches have different durability, consistency, lifecycle, backup, and access semantics.

### 5. Security and Configuration Layer

These concepts define trust, access, and runtime parameters:

- identity principal
- access policy
- configuration and secrets

This layer is necessary because vendor lock-in is not limited to compute and storage. Identity and permission models are among the least portable parts of cloud systems.

### 6. Placement and Resilience Layer

These concepts define where and how reliably infrastructure runs:

- placement and availability
- scaling policy, where scaling affects resilience
- capacity profile, where capacity affects deployability and performance

Placement and availability deserve explicit representation because region, zone, fault domain, redundancy, and managed-service availability guarantees cannot be assumed equivalent across providers.

## Why These Concepts Are Minimal

The refined concept set is minimal in a research sense because removing any concept weakens either expressiveness, portability analysis, or backend lowering:

- Without deployment unit, the infrastructure has no unit of compilation, validation, or output.
- Without deployment interface, the DSL cannot represent reusable inputs, outputs, and environment-specific values.
- Without workload, the language cannot describe executable infrastructure.
- Without runtime and artifact model, it cannot distinguish VM, container, function, job, image, and package semantics.
- Without capacity profile, provider lowerers must guess instance size, resource limits, storage size, or service tier.
- Without network boundary, it cannot describe private services, isolation, or routing constraints.
- Without endpoint, it cannot describe externally reachable APIs, internal services, or load-balanced access.
- Without storage, it cannot describe object assets, volumes, filesystems, or persistent data.
- Without data service, it cannot model managed PostgreSQL and similar stateful services.
- Without identity principal and access policy, it cannot represent security intent independently of IAM/RBAC implementations.
- Without configuration and secrets, it cannot model real deployable workloads.
- Without scaling policy, it cannot represent elasticity, which is one of the core properties of cloud computing.
- Without placement and availability, it cannot reason about regions, zones, redundancy, or disaster tolerance.
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
| Cost and billing controls | Important governance feature, but provider billing models differ substantially | Future extension or metadata/policy layer |

This supports the thesis novelty from `Outcomes.md`: the project should not claim that every cloud feature is portable. It should identify non-portable features before deployment and explain their consequences.

## Initial DSL Construct Proposal

The refined concept set can be expressed through the following initial DSL construct families:

```text
deploy <name> {
  input <name> { ... }
  output <name> { ... }

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
| B2: Web app with managed PostgreSQL | workload, runtime/artifact, data service, network, secrets, policy, capacity | Yes |
| B3: Autoscaled API service | workload, endpoint, scaling, network, capacity | Yes |
| B4: Private service with internal database | workload, data service, network boundary, relation, policy | Yes |
| B5: Serverless API with storage | workload, runtime model, artifact, endpoint, storage, policy | Yes, if runtime includes `function` |
| B6: Kubernetes-hosted app with external database | workload, runtime/artifact, data service, endpoint, network, configuration, identity | Yes |

This gives a preliminary expressiveness result:

```text
Benchmark coverage: 6 / 6 representative cases
Preliminary expressiveness: 100 percent for selected benchmark scope
```

This result should be interpreted cautiously. It means the concept set can describe the selected benchmark architectures at the DSL level. It does not prove that every backend can preserve all semantics exactly. Backend-specific semantic fidelity must be evaluated under RQ4 and RQ5.

## Critical Analysis of the Revised Answer

The revised concept set is stronger than the initial answer because it separates three concerns that were previously hidden inside other concepts:

- Deployment interface is separated from deployment unit.
- Runtime and artifact are separated from generic workload.
- Capacity profile is separated from scaling and placement.

This makes the DSL more suitable for compiler design. A compiler needs explicit inputs, outputs, artifacts, capacity constraints, and relationships in order to produce backend-specific IaC predictably.

However, the revised set also introduces a tradeoff. A larger concept set may make the DSL less minimal in a strict numerical sense. The research justification is that these are not arbitrary features; they are semantic concepts repeatedly present in standards and IaC systems. Their inclusion reduces hidden assumptions during backend lowering.

The most uncertain concepts are:

- data service: useful for managed PostgreSQL and future services, but difficult to generalize across all PaaS offerings.
- identity and access policy: portable at the intent level, but often only partially portable at the implementation level.
- placement and availability: necessary for resilience, but provider availability-zone semantics and managed-service guarantees differ.
- capacity profile: necessary for backend generation, but mappings from abstract capacity to provider SKUs may be approximate.

These uncertainties do not invalidate the RQ1 result. They show where RQ2 and RQ3 must be careful. The IR must preserve these concepts as semantic intent, and provider mappings must classify results as exact, partial, degraded, unsupported, or provider-specific.

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
| Portable core | deployment unit, deployment interface, workload, relationship/lifecycle, endpoint | High |
| Portable with semantic variation | runtime/artifact, capacity, storage, data service, scaling, placement/availability | Medium |
| Portable but provider-sensitive | identity principal, access policy, secrets | Medium to low |
| Non-portable extension | provider-specific IAM conditions, load balancer features, proprietary managed services, engine-specific macros | Low |

This taxonomy should become an input to RQ2, where the semantic IR will represent these concepts as nodes, edges, attributes, constraints, and portability annotations.

## Research Finding

The first research question can be answered with the following finding:

> The minimal concept set for a vendor-neutral cloud architecture DSL consists of deployment unit, deployment interface, workload, runtime and artifact model, capacity profile, network boundary, endpoint, storage, data service, identity principal, access policy, configuration/secrets, scaling policy, placement/availability, and relationship/lifecycle semantics. These concepts are sufficient to express the selected benchmark architectures without using provider-specific resource names, while preserving enough semantic information for IR construction, provider capability mapping, backend lowering, and portability diagnostics.

## Implication for the Next Research Question

RQ1 identifies what the DSL must express. RQ2 must now define how these concepts are represented internally.

The next step is to transform the concept set into a semantic IR with:

- typed nodes for each infrastructure concept
- typed edges for dependency, reachability, binding, ownership, policy, requirement, and capability relations
- attributes for required behavior
- constraints for validation
- inputs and outputs for the deployment interface
- artifact references for runnable workloads
- portability annotations for exact, partial, degraded, unsupported, and provider-extension mappings

This connects the RQ1 answer directly to the planned IR contribution of the thesis.

## References

[1] Mell, P. and Grance, T., "The NIST Definition of Cloud Computing," NIST SP 800-145, 2011. https://csrc.nist.gov/pubs/sp/800/145/final

[2] Liu, F. et al., "NIST Cloud Computing Reference Architecture," NIST SP 500-292, 2011. https://www.nist.gov/publications/nist-cloud-computing-reference-architecture

[3] OASIS, "Topology and Orchestration Specification for Cloud Applications Version 2.0." https://docs.oasis-open.org/tosca/TOSCA/v2.0/csd06/TOSCA-v2.0-csd06.html

[4] Breitenbucher, U. et al., "Model-Based Cloud Resource Management with TOSCA and OCCI," arXiv, 2020. https://arxiv.org/abs/2001.07900

[5] HashiCorp, "Terraform Language Documentation." https://developer.hashicorp.com/terraform/language

[6] AWS, "CloudFormation template sections." https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

[7] Microsoft Learn, "What are ARM templates?" https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview

[8] OpenStack, "Introduction to Heat Orchestration." https://static.openstack.org/docs/heat/latest/admin/introduction.html

[9] Kubernetes, "Workloads." https://kubernetes.io/docs/concepts/workloads/

[10] Kubernetes, "Volumes." https://kubernetes.io/docs/concepts/storage/volumes/

[11] SPIFFE, "Secure Production Identity Framework for Everyone." https://spiffe.io/docs/latest/spiffe-specs/spiffe/
