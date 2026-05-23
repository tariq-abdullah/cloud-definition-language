# Existing Literature Study: Unified Cloud Architecture Definition Language

Prepared from `synopsis.md` and `outcomes.md`. Literature checked on 23 May 2026.

## 1. Research Context

The proposed thesis studies the design and implementation of a vendor-neutral cloud architecture definition language. The core idea is to let users describe cloud infrastructure intent once, translate that intent into a semantic Intermediate Representation (IR), and lower the IR into provider-specific Infrastructure-as-Code (IaC) backends such as Terraform, AWS CloudFormation, Azure Bicep, Kubernetes manifests, or OpenStack templates.

The important research claim is not simply "support multiple clouds." Existing tools already claim multi-cloud support in different ways. The stronger claim is semantic portability: representing cloud infrastructure in a form that preserves intended behavior across providers, identifies where equivalent capabilities do not exist, and reports the expected degradation, risk, or migration effort before deployment.

The research therefore sits at the intersection of cloud computing, compiler design, model-driven engineering, programming languages, IaC analysis, and distributed systems.

## 2. Current State of Existing Work

### 2.1 Provider-native Infrastructure-as-Code

Provider-native IaC tools remain the most mature way to provision cloud infrastructure. AWS CloudFormation models AWS resources as stacks and provisions AWS infrastructure predictably using templates [4]. Azure Bicep provides a declarative DSL over Azure Resource Manager templates and is explicitly designed for deploying Azure resources [5]. AWS CDK raises the abstraction level by letting developers model AWS infrastructure with constructs in familiar programming languages, but its deployment target remains AWS CloudFormation [6].

These tools are valuable because they expose provider capabilities early and accurately. They usually support new services, resource types, and provider-specific features faster than neutral abstractions. Their limitation is structural: the abstraction boundary is the provider itself. A CloudFormation template, a Bicep file, or an AWS CDK construct tree encodes a provider-specific resource model, dependency model, identity model, network model, and lifecycle behavior.

For this thesis, provider-native tools are best understood as backend targets rather than competitors. They provide strong operational semantics inside one ecosystem, but they do not solve portability at the intent level. If a user describes `AWS::RDS::DBInstance`, `Microsoft.DBforPostgreSQL/flexibleServers`, or a provider-specific load balancer rule, the portability problem has already been pushed onto the user.

### 2.2 Multi-provider IaC Engines

Terraform is the dominant multi-provider IaC engine. Its provider architecture separates Terraform Core from provider plugins, and each provider contributes resource types and data sources [3]. OpenTofu continues the Terraform model as a Linux Foundation governed fork and advertises compatibility with existing Terraform workflows and configurations [8]. Pulumi offers another major path: infrastructure is authored in general-purpose languages such as TypeScript, Python, Go, .NET, Java, and YAML, with packages for many providers [7].

These systems reduce operational fragmentation. They provide a shared workflow for planning, state management, dependency ordering, and deployment across many clouds. However, their "multi-cloud" nature is mostly at the engine and workflow level, not at the semantic resource level. Terraform can manage AWS, Azure, Google Cloud, Kubernetes, and SaaS resources, but an `aws_instance` is not semantically the same object as an `azurerm_linux_virtual_machine`, and Terraform does not infer a portable compute intent from both. Pulumi improves expressiveness by using programming languages, but provider packages still expose provider-specific APIs.

The deprecation of CDK for Terraform, announced by HashiCorp for 10 December 2025, is also instructive [9]. It shows that adding a programming language layer above existing provider schemas does not automatically create a durable semantic abstraction. It may improve developer ergonomics, but the generated target still reflects the provider schema underneath.

The research gap here is clear: multi-provider IaC engines standardize execution, but they do not provide a formal semantic IR for equivalence, partial equivalence, provider capability mapping, or degradation analysis.

### 2.3 Model-driven and Standard-based Cloud Description Languages

TOSCA is the most significant open standard in this area. The TOSCA v2.0 specification describes application components and their relationships through service topology and lifecycle orchestration processes [1]. OASIS approved TOSCA v2.0 as an OASIS Standard on 9 September 2025, emphasizing broader applicability beyond traditional cloud infrastructure [2].

TOSCA demonstrates that topology and lifecycle can be modeled in a portable language. It is important prior art because it treats cloud applications as graphs of nodes and relationships, not just collections of provider resources. This aligns strongly with the thesis outcome of an intent -> semantic graph -> provider lowering pipeline.

However, TOSCA's strength is also its limitation for this thesis. It is a general orchestration standard, not specifically a compiler-oriented IR for translating intent into modern IaC backends with explicit semantic fidelity scoring. TOSCA models topology and lifecycle, but the problem of translating provider-independent intent into provider-specific constructs while measuring what changed is still not solved in a fine-grained, compiler-style way.

CAMEL is another important model-driven approach. It was developed to support multi-cloud application design, deployment, and adaptation in the PaaSage ecosystem [10]. CAMEL integrates multiple domain-specific languages to describe provisioning, deployment, requirements, scalability, security, organization, and execution contexts. It shows that multi-cloud modeling requires more than resources: it needs deployment constraints, metrics, adaptation rules, and runtime context.

DOML, the DevOps Modeling Language, is more recent and directly related. It proposes a cloud modeling language that can be mapped into multiple IaC languages, addressing provisioning, deployment, and configuration [11]. DOML validates the need for a single modeling paradigm to reduce the expertise required across specialized IaC languages.

These approaches support the thesis direction but leave space for a more compiler-centered contribution. The proposed work can focus less on creating a broad modeling ecosystem and more on a precise IR, lowering rules, semantic equivalence, and portability diagnostics.

### 2.4 Kubernetes-native Control Planes and Platform Abstractions

Kubernetes introduced a widely adopted declarative model for desired state and reconciliation. Crossplane extends that model to cloud infrastructure by letting teams build custom APIs and control planes using compositions and managed resources [12]. A platform team can expose a simplified API such as `Database`, `App`, or `Environment` while Crossplane composes underlying resources.

This is powerful because it shifts abstraction design from tool vendors to platform teams. It also makes infrastructure part of a continuously reconciled control plane rather than only a one-time provisioning plan.

However, Crossplane is not primarily a vendor-neutral cloud compiler. It is a control-plane framework. Its abstractions are usually organization-specific APIs backed by provider-specific managed resources. Crossplane can hide provider details from application teams, but it does not inherently define a universal semantic IR or measure portability between providers. The portability semantics live in the platform team's compositions, not in a formal language core.

Open Application Model (OAM) is also relevant because it attempted to separate application components from operational traits in a platform-agnostic way [13]. Its influence appears in tools such as KubeVela, but the broader ecosystem has moved more toward Kubernetes-native platform engineering patterns than toward one universal application model.

For this thesis, Kubernetes and Crossplane are useful reference points for desired state, reconciliation, custom APIs, and composition, but the proposed compiler should remain backend-agnostic. Kubernetes may be one target, not the assumed runtime for all infrastructure.

### 2.5 Infrastructure-from-Code and Cloud Programming Languages

Infrastructure-from-Code (IfC) and cloud programming languages represent a newer wave of abstraction. Instead of writing separate IaC files, developers express infrastructure through application code, annotations, SDKs, or cloud-oriented languages. A 2024 empirical study describes IfC as an advancement of IaC in which higher-level programming languages or SDKs generate provisioning and deployment configurations automatically [14].

This direction is important because it recognizes that current IaC demands too much cloud-specific expertise from developers. It also shows demand for higher-level abstractions and compiler pipelines.

The limitation is that IfC usually begins from application source code, not from an explicit infrastructure intent language. This can improve productivity, but it can also make generated infrastructure harder to trace and reason about. The empirical study found that tracing generated infrastructure configurations from code can be challenging, especially in SDK-based approaches [14]. For research into cloud portability, traceability is not optional: users need to see why a provider mapping was chosen, what semantics were preserved, and what was degraded.

The proposed DSL therefore occupies a useful middle ground. It is higher-level than Terraform or CloudFormation, but more explicit and analyzable than infrastructure inferred from arbitrary application code.

### 2.6 IaC Analysis, Testing, and Benchmarking

IaC research increasingly treats infrastructure definitions as software artifacts that can contain defects, security weaknesses, maintainability problems, and semantic errors. A 2022 survey of static analysis for IaC notes that IaC scripts can introduce reliability and security issues in deployed infrastructure and reviews defect categories and analysis techniques [15]. Later work has studied defect taxonomies, state reconciliation problems, security practices, and IaC quality.

More recent research also shows that the next frontier is not only generating syntactically valid IaC. Multi-IaC-Eval, a 2025 benchmark, found that modern LLMs can often generate syntactically valid IaC across formats, but semantic alignment and complex infrastructure patterns remain difficult [16]. Similarly, 2026 work on metamorphic testing for IaC engines argues that IaC engines such as Terraform, OpenTofu, and Pulumi are critical infrastructure systems, while prior work has mostly focused on IaC program correctness rather than engine correctness [17].

This literature strongly supports the thesis. If syntax validity is no longer the main problem, the research problem shifts toward semantics: does the generated infrastructure mean the same thing, behave the same way, satisfy the same constraints, and fail in predictable ways when a provider lacks a feature?

## 3. Critical Synthesis

| Existing approach | Representative systems | Main contribution | Limitation for this thesis topic |
| --- | --- | --- | --- |
| Provider-native IaC | CloudFormation, Bicep, AWS CDK | Accurate access to provider-specific resources and lifecycle behavior | Locks the model to one provider's vocabulary and semantics |
| Multi-provider IaC engines | Terraform, OpenTofu, Pulumi | Shared workflow across many providers; strong ecosystem | Provider resources remain provider-specific; no built-in semantic equivalence model |
| Standard orchestration languages | TOSCA | Topology and lifecycle modeling across domains | Broad standard, but not a focused compiler IR with portability scoring and backend lowering diagnostics |
| Model-driven multi-cloud languages | CAMEL, DOML, MODAClouds/CloudML family | Higher-level modeling of deployments, requirements, and multi-cloud concerns | Often complex, ecosystem-dependent, or not centered on modern IaC backend generation |
| Kubernetes-native platform abstractions | Crossplane, OAM/KubeVela patterns | Custom APIs, composition, reconciliation, platform self-service | Semantics are encoded by platform teams; portability analysis is not the core language feature |
| Infrastructure-from-Code | Cloud programming languages, SDK-based IfC | Derives infrastructure from application code; improves developer ergonomics | Traceability and explicit portability reasoning can be weak |
| IaC analysis and testing | Static analysis, Multi-IaC-Eval, metamorphic IaC engine testing | Detects defects, validates syntax, benchmarks generation, tests engines | Usually analyzes existing IaC rather than compiling provider-neutral intent into semantically comparable targets |

The central observation is that existing work solves pieces of the problem, but not the whole research problem. Provider-native IaC solves accuracy. Terraform and Pulumi solve multi-provider workflow. TOSCA and CAMEL solve model-driven description. Crossplane solves platform API composition. Static analysis solves quality checks. What remains underdeveloped is a unified compiler architecture for cloud intent with a semantic IR that can reason about equivalence, non-equivalence, and acceptable degradation across providers.

## 4. Research Gap

The literature reveals six concrete gaps.

1. Lack of intent-level abstraction.
Most current IaC begins from provider resources. Even when the syntax is neutral, the schema is provider-specific. A research DSL should let users express concepts such as `PortableComputeNode`, `ManagedPostgres`, `ObjectStorage`, `PublicEndpoint`, `PrivateNetwork`, `AutoscalingPolicy`, and `IdentityBoundary` without prematurely choosing AWS, Azure, GCP, Kubernetes, or OpenStack.

2. Lack of a semantic IR.
Existing IaC engines maintain dependency graphs and state, but they do not expose an IR that captures cloud semantics such as durability guarantees, network reachability, scaling behavior, identity constraints, data locality, lifecycle policy, and managed-service assumptions. Without such an IR, translation is mostly schema mapping rather than compiler-like semantic lowering.

3. Weak handling of partial equivalence.
Cloud services rarely match exactly. For example, managed PostgreSQL, object storage, IAM, load balancing, serverless functions, and autoscaling all differ across providers. Current tools can deploy alternatives, but they generally do not explain whether the alternative preserves semantics, weakens a guarantee, changes failure behavior, or requires manual intervention.

4. Limited portability scoring before deployment.
The thesis outcome of a portability analysis engine is especially valuable. Existing tools can validate syntax, plan changes, scan security rules, or detect policy violations. Fewer tools estimate migration difficulty from semantic features before deployment. A portability score backed by explicit non-portable features would make architecture decisions measurable.

5. Separation between modeling research and production IaC ecosystems.
Research languages such as CAMEL, DOML, and TOSCA-inspired systems show strong modeling ideas, but production teams often standardize on Terraform, CloudFormation, Bicep, Kubernetes, or Pulumi. A successful research prototype should bridge these worlds by keeping the semantic model research-grade while lowering to existing IaC backends.

6. Need for evaluation beyond syntactic correctness.
IaC generation research shows that syntactically valid output is insufficient. A thesis evaluation should include portability, semantic fidelity, expressiveness, explainability, and backend correctness. This aligns with the objectives in the synopsis: portability, semantic fidelity, and expressiveness.

## 5. Why This Research Is Needed

This research is needed because the cloud ecosystem is becoming more heterogeneous, not less. Enterprises use combinations of public cloud, private cloud, Kubernetes, SaaS services, and edge environments. A 2023 systematic literature review of multi-cloud native applications notes that single-provider cloud use is no longer sufficient for many scenarios, and that heterogeneity of architectures, methods, tools, and frameworks remains a major challenge [18].

It is also needed because vendor lock-in is not only a business issue. It is encoded technically in APIs, resource schemas, identity systems, networking constructs, managed service behavior, and operational tooling. Research on cloud vendor lock-in identifies portability and interoperability as continuing concerns and argues that lock-in cannot be fully eliminated, only mitigated through better approaches across infrastructure, platform, and application layers [19].

The proposed research is timely for four reasons.

First, IaC has become critical infrastructure. Terraform, OpenTofu, Pulumi, CloudFormation, and Bicep are no longer convenience scripts; they control production systems. As a result, a semantic error in infrastructure code can become a security incident, outage, cost spike, or data exposure.

Second, the industry is moving toward higher-level platform abstractions, but most organizations build those abstractions locally and inconsistently. Crossplane and platform engineering patterns show that teams want custom infrastructure APIs, but there is still no widely adopted semantic language for portable cloud architecture intent.

Third, AI-assisted IaC generation increases the need for formal semantics. If AI systems generate Terraform, Bicep, CloudFormation, or Kubernetes manifests, then validation must go beyond "does it parse?" The generated architecture must match intent. A semantic IR can provide the intermediate structure needed for verification, explanation, and repair.

Fourth, multi-cloud strategy is often poorly measured. Organizations may say they want portability, but they lack a way to quantify which features are portable, which are provider-specific, and how much behavior changes during migration. A DSL compiler with capability mapping and portability scoring can convert portability from a slogan into an engineering metric.

## 6. Positioning of the Proposed Thesis

The proposed thesis should position itself as a compiler and semantic analysis project for cloud infrastructure, not merely as a DevOps automation project.

The strongest research contribution can be framed as:

```text
Portable cloud intent DSL
    -> Semantic IR
    -> Provider capability mapping
    -> Backend lowering
    -> Fidelity and portability diagnostics
```

This framing creates a clear distinction from existing tools.

Terraform and Pulumi are execution engines and ecosystems. The proposed system is an intent compiler.

TOSCA is a broad orchestration standard. The proposed system is a focused, evaluable compiler architecture for modern IaC targets.

Crossplane is a control-plane framework. The proposed system is a language and IR that can target Crossplane or Kubernetes, but does not require Kubernetes as the universal abstraction layer.

DOML and CAMEL show that multi-cloud modeling is feasible. The proposed work can add a stronger compiler design contribution by defining IR semantics, lowering rules, and fidelity metrics.

## 7. Suggested Research Questions

RQ1: What minimal set of cloud infrastructure concepts can express common portable architectures across multiple providers without collapsing into provider-specific resources?

RQ2: How should a semantic IR represent cloud resource intent, dependencies, constraints, capabilities, and lifecycle behavior?

RQ3: How can provider capability mappings be defined so that exact matches, partial matches, unsupported features, and degraded alternatives are distinguishable?

RQ4: How accurately can the compiler lower a single DSL specification into multiple IaC backends while preserving behavior?

RQ5: What metrics can evaluate portability, semantic fidelity, expressiveness, and explainability of generated IaC?

## 8. Evaluation Direction

A credible evaluation framework should include:

- Portability: percentage of DSL features that can be mapped exactly or approximately to each provider/backend.
- Semantic fidelity: how closely the generated backend preserves compute, storage, network, identity, scaling, and lifecycle semantics.
- Expressiveness: whether the DSL can model representative architectures such as web app plus database, static website plus object storage, private network plus compute tier, serverless API, and Kubernetes-hosted service.
- Diagnostic quality: whether unsupported or degraded mappings are explained in a useful way.
- Backend validity: whether generated Terraform, CloudFormation, Bicep, Kubernetes, or OpenStack output passes syntax validation and produces expected resource graphs.
- Migration insight: whether the system identifies non-portable features before deployment.

The evaluation should avoid claiming universal portability. A stronger research stance is to make portability explicit, measurable, and explainable, including cases where portability fails.

## 9. Conclusion

The existing literature confirms that cloud portability is an old problem, but current industry conditions make it newly urgent. Provider-native IaC is precise but locked to one ecosystem. Multi-provider IaC engines standardize workflow but preserve provider-specific resource schemas. Model-driven languages and standards demonstrate the value of topology and multi-cloud modeling, but they have not become the default bridge to modern IaC practice. Kubernetes-native control planes show a path toward custom abstractions, but portability semantics remain local to each platform team.

The research need is therefore well founded: a vendor-neutral cloud architecture DSL with a semantic IR can fill the gap between high-level architecture intent and provider-specific IaC. Its novelty should be measured not by the number of backends it supports, but by how well it preserves, explains, and quantifies infrastructure semantics across those backends.

## References

[1] OASIS, "Topology and Orchestration Specification for Cloud Applications Version 2.0." https://docs.oasis-open.org/tosca/TOSCA/v2.0/cs01/TOSCA-v2.0-cs01.html

[2] OASIS Open, "OASIS Approves TOSCA V2.0 Standard for Cloud Orchestration," 9 September 2025. https://www.oasis-open.org/2025/09/09/oasis-approves-tosca-v2-0-standard-for-cloud-orchestration/

[3] HashiCorp, "Terraform Providers." https://developer.hashicorp.com/terraform/language/providers

[4] AWS, "AWS CloudFormation Documentation." https://docs.aws.amazon.com/cloudformation/

[5] Microsoft Learn, "What is Bicep?" https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview

[6] AWS, "AWS Cloud Development Kit Documentation." https://aws.amazon.com/documentation-overview/cdk/

[7] Pulumi, "Pulumi Documentation." https://www.pulumi.com/docs/

[8] OpenTofu, "Open-Source Infrastructure as Code." https://opentofu.org/

[9] HashiCorp, "CDK for Terraform." https://developer.hashicorp.com/terraform/cdktf

[10] Achilleos et al., "The cloud application modelling and execution language," Journal of Cloud Computing, 2019. https://link.springer.com/article/10.1186/s13677-019-0138-7

[11] Rademacher et al., "DOML: A new modeling approach to Infrastructure-as-Code," Future Generation Computer Systems, 2024. https://www.sciencedirect.com/science/article/pii/S0306437924000802

[12] Crossplane, "What's Crossplane?" https://docs.crossplane.io/latest/whats-crossplane/

[13] Microsoft Open Source Blog, "Announcing the Open Application Model," 2019. https://opensource.microsoft.com/blog/2019/10/16/announcing-open-application-model/

[14] Simhandl and Zdun, "Cloud Programming Languages and Infrastructure From Code: An Empirical Study," SLE 2024. https://eprints.cs.univie.ac.at/8131/

[15] Chiari, De Pascalis, and Pradella, "Static Analysis of Infrastructure as Code: a Survey," ICSA-C 2022. https://arxiv.org/abs/2206.10344

[16] Davidson et al., "Multi-IaC-Eval: Benchmarking Cloud Infrastructure as Code Across Multiple Formats," arXiv, 2025. https://arxiv.org/abs/2509.05303

[17] Spielmann et al., "Metamorphic Testing for Infrastructure-as-Code Engines," OOPSLA 2026. https://2026.splashcon.org/details/oopsla-2026/27/Metamorphic-Testing-for-Infrastructure-as-Code-Engines

[18] Alonso et al., "Understanding the challenges and novel architectural models of multi-cloud native applications: a systematic literature review," Journal of Cloud Computing, 2023. https://link.springer.com/article/10.1186/s13677-022-00367-6

[19] Opara-Martins, Sahandi, and Tian, "Critical analysis of vendor lock-in and its impact on cloud computing migration," Journal of Cloud Computing, 2016. https://link.springer.com/article/10.1186/s13677-016-0054-z
