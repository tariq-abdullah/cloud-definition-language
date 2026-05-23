# Cloud Definition Language

> A research project on semantic portability for cloud infrastructure.

This repository contains the research foundation and development plan for an M.Tech thesis titled:

**Design and Implementation of a Unified Cloud Architecture Definition Language: A Semantic Compiler Framework for Portable, Vendor-Neutral Infrastructure-as-Code Generation, Capability Mapping, and Multi-Cloud Deployment Analysis**

## Researcher

**Researcher:** Tariq Abdullah  
**Programme:** M.Tech in Cloud Computing  
**Institution:** Indian Institute of Technology Patna (IIT Patna)  
**Date:** January 2025  
**Submission note:** Prepared in partial fulfilment of the requirements for the degree of Master of Technology in Cloud Computing.

The project investigates how cloud infrastructure can be described once as provider-neutral intent, transformed into a semantic Intermediate Representation (IR), and lowered into provider-specific Infrastructure-as-Code (IaC) backends with explicit portability and fidelity diagnostics.

## Research Motivation

Modern cloud infrastructure is usually written in provider-native or provider-shaped tools such as CloudFormation, Bicep, Terraform provider schemas, Pulumi provider packages, and Kubernetes manifests. These tools are powerful, but they often bind architecture definitions to a specific provider's resources, identity model, networking model, lifecycle behavior, and managed-service assumptions.

This creates a portability problem. Migrating or reproducing the same architecture across cloud providers usually requires manual rewriting, reinterpretation of intent, and careful checking of changed behavior.

This thesis treats cloud portability as a compiler and semantic modelling problem:

```text
Portable cloud intent
  -> Semantic IR
  -> Provider capability mapping
  -> Backend-specific IaC generation
  -> Portability and fidelity diagnostics
```

## Core Idea

The proposed system is a vendor-neutral cloud architecture definition language supported by:

- a domain-specific language for cloud infrastructure intent;
- a semantic IR for representing resources, relationships, constraints, and lifecycle behavior;
- provider capability mappings for exact, composed, partial, degraded, unsupported, and provider-specific features;
- backend lowerers for generating IaC outputs;
- diagnostics for portability, semantic fidelity, and explainability.

The goal is not to pretend that every cloud feature is portable. The goal is to make portability explicit, measurable, and explainable before deployment.

## Research Questions

**RQ1:** What minimal set of cloud infrastructure concepts can express common portable architectures across multiple providers without collapsing into provider-specific resources?

**RQ2:** How should a semantic IR represent cloud resource intent, dependencies, constraints, capabilities, and lifecycle behavior?

**RQ3:** How can provider capability mappings be defined so that exact matches, partial matches, unsupported features, and degraded alternatives are distinguishable?

**RQ4:** How accurately can the compiler lower a single DSL specification into multiple IaC backends while preserving behavior?

**RQ5:** What metrics can evaluate portability, semantic fidelity, expressiveness, and explainability of generated IaC?

## Current RQ1 Finding

The initial RQ1 literature review identifies the following semantic portability kernel:

- deployment unit
- deployment interface
- workload/component
- runtime and artifact model
- capacity profile
- network boundary
- endpoint
- storage
- data service
- identity principal
- access policy
- configuration and secrets
- scaling/adaptation policy
- placement and availability
- requirements/SLO constraints
- relationship and lifecycle semantics

These concepts form the proposed minimal core for a vendor-neutral cloud architecture DSL.

## Example Direction

An early DSL direction is:

```text
deploy web_app {
  requirement production_slo {
    availability: "99.9%"
    max_latency_ms: 250
    cost_class: balanced
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
  }

  data_service app_db {
    kind: relational_database
    engine: postgres
    mode: managed
    network: private_net
  }

  endpoint public_api {
    target: api
    protocol: https
    exposure: public
  }
}
```

The DSL avoids provider-specific resources while preserving enough semantic information for IR construction, backend lowering, and portability diagnostics.

## Repository Structure

| File | Purpose |
| --- | --- |
| [Synopsis.md](Synopsis.md) | Initial thesis synopsis, title, problem statement, and objectives |
| [Outcomes.md](Outcomes.md) | Expected outcomes, novelty, and broader impact of the work |
| [Existing literature.md](Existing%20literature.md) | Literature review, research gap, and thesis positioning |
| [work plan and methodology.md](work%20plan%20and%20methodology.md) | Research methodology, phases, evaluation metrics, and work plan |
| [RQ1 research results.md](RQ1%20research%20results.md) | Peer-reviewed RQ1 literature review and refined answer |
| [Final thesis statement.md](Final%20thesis%20statement.md) | Consolidated final thesis positioning statement |
| [Credits.md](Credits.md) | Tool acknowledgement |
| [LICENSE](LICENSE) | MIT License |

## Project Status

This repository is currently in the research design and thesis-planning phase.

Current progress:

- [x] Problem definition
- [x] Expected outcomes
- [x] Existing literature study
- [x] Research gap and positioning
- [x] Work plan and methodology
- [x] RQ1 literature review and preliminary answer
- [x] Final thesis statement
- [ ] DSL grammar and syntax specification
- [ ] Semantic IR schema
- [ ] Provider capability mapping format
- [ ] Compiler prototype
- [ ] Backend IaC generation
- [ ] Evaluation and benchmark results

## Planned Prototype Scope

The planned compiler prototype will focus on:

- DSL frontend;
- AST and semantic validation;
- JSON/YAML IR output;
- provider/backend capability profiles;
- Terraform/OpenTofu backend generation;
- Kubernetes manifest backend generation;
- portability and semantic fidelity reports.

## Evaluation Direction

The proposed evaluation will use benchmark cloud architectures such as:

- static website with object storage;
- web application with managed PostgreSQL;
- autoscaled API service;
- private service with internal database;
- serverless API with storage;
- Kubernetes-hosted application with external database.

Evaluation metrics include:

- portability score;
- semantic fidelity score;
- expressiveness score;
- backend validity;
- diagnostic quality.

## Academic Context

This work is part of an M.Tech in Cloud Computing thesis project by Tariq Abdullah at IIT Patna. The research is positioned at the intersection of:

- cloud computing;
- compiler design;
- infrastructure-as-code;
- model-driven engineering;
- distributed systems;
- programming languages.

## License

This repository is licensed under the [MIT License](LICENSE).

## Author

Tariq Abdullah  
M.Tech in Cloud Computing  
IIT Patna
