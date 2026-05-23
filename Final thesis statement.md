# Final Thesis Statement

## Proposed Title

**Design and Implementation of a Unified Cloud Architecture Definition Language: A Semantic Compiler Framework for Portable, Vendor-Neutral Infrastructure-as-Code Generation, Capability Mapping, and Multi-Cloud Deployment Analysis**

## Researcher and Programme Details

**Researcher:** Tariq Abdullah  
**Programme:** M.Tech in Cloud Computing  
**Institution:** Indian Institute of Technology Patna (IIT Patna)  
**Date:** January 2025  
**Submission note:** Prepared in partial fulfilment of the requirements for the degree of Master of Technology in Cloud Computing.

## Core Thesis Statement

This thesis argues that cloud infrastructure portability cannot be solved only by using a common deployment tool or by translating one provider-specific configuration format into another. True portability requires a higher-level semantic abstraction in which cloud architecture is described as provider-neutral intent, represented through an intermediate semantic model, and then lowered into provider-specific Infrastructure-as-Code outputs with explicit analysis of preserved, partially preserved, degraded, and unsupported semantics.

The proposed research therefore designs and implements a unified cloud architecture definition language supported by a semantic Intermediate Representation (IR), provider capability mappings, and a prototype compiler pipeline. The central claim is that a vendor-neutral DSL can express common cloud architectures without depending on provider-specific resource names, while a semantic IR can preserve enough infrastructure meaning to generate backend-specific IaC and report portability limitations before deployment.

## Research Problem

Modern cloud infrastructure is usually defined through provider-native or provider-shaped tools such as CloudFormation, Bicep, Terraform provider schemas, Pulumi provider packages, Kubernetes manifests, and other IaC systems. These tools are powerful and operationally mature, but they expose infrastructure primarily through the vocabulary and semantics of specific platforms. As a result, a cloud architecture often becomes coupled to a provider's resource model, identity system, network model, lifecycle behavior, and managed-service assumptions.

This coupling creates a significant portability problem. When an organization wants to migrate from one provider to another, support multiple deployment targets, compare provider alternatives, or reduce vendor lock-in, the existing infrastructure definition often cannot be reused directly. Even when tools support multiple providers, users usually need to rewrite resource definitions, reinterpret architectural intent, and manually identify which behavior has changed.

The research problem is therefore not simply how to automate cloud deployment. The deeper problem is how to represent cloud infrastructure intent in a form that is independent of any single provider while still being precise enough to compile into real IaC backends and evaluate semantic fidelity.

## Research Gap

Existing work addresses parts of this problem but leaves a clear gap.

Provider-native IaC tools provide accurate access to one cloud ecosystem but do not support provider-neutral infrastructure intent. Multi-provider IaC engines provide a common workflow across clouds, but their schemas remain provider-specific. Model-driven approaches such as TOSCA, CAMEL, CloudMF, and DOML show that cloud applications can be modelled at higher levels of abstraction, but they do not fully solve the compiler-style problem of translating provider-neutral cloud intent into modern IaC backends while measuring semantic preservation and degradation. Kubernetes-native control planes and platform abstractions support custom APIs and reconciliation, but portability semantics are usually encoded locally by platform teams rather than by a formal language core.

The gap this thesis addresses is the absence of a focused compiler architecture for cloud infrastructure portability: a language and IR that can represent infrastructure intent, reason about provider capability differences, generate backend-specific IaC, and explain where portability succeeds or fails.

## Aim

The aim of this thesis is to design and implement a vendor-neutral cloud architecture definition language and compiler framework that allows users to describe common cloud infrastructure architectures once, transform them into a semantic IR, lower them into multiple IaC backends, and evaluate the portability and semantic fidelity of the generated outputs.

## Objectives

The objectives of the study are:

1. To define a minimal set of provider-neutral cloud infrastructure concepts required to express common portable architectures.
2. To design a domain-specific language that represents cloud infrastructure intent without depending on provider-specific resource names.
3. To develop a semantic IR capable of representing resources, relationships, constraints, dependencies, lifecycle behavior, and portability annotations.
4. To define provider capability mappings that distinguish exact, composed, partial, degraded, unsupported, and provider-specific features.
5. To implement a prototype compiler pipeline that translates DSL specifications into one or more backend IaC formats.
6. To evaluate the prototype using benchmark architectures and metrics for portability, semantic fidelity, expressiveness, backend validity, and diagnostic quality.

## Research Questions

The thesis is guided by the following research questions:

**RQ1:** What minimal set of cloud infrastructure concepts can express common portable architectures across multiple providers without collapsing into provider-specific resources?

**RQ2:** How should a semantic IR represent cloud resource intent, dependencies, constraints, capabilities, and lifecycle behavior?

**RQ3:** How can provider capability mappings be defined so that exact matches, partial matches, unsupported features, and degraded alternatives are distinguishable?

**RQ4:** How accurately can the compiler lower a single DSL specification into multiple IaC backends while preserving behavior?

**RQ5:** What metrics can evaluate portability, semantic fidelity, expressiveness, and explainability of generated IaC?

## Current RQ1 Position

The first research question has been initially answered through the existing literature study and peer-reviewed RQ1 review. The minimal portable concept set is not a list of provider resources. It is a semantic portability kernel consisting of:

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

This set is minimal in the sense that removing any concept would weaken the language's ability to express common architectures, preserve semantic intent, or support backend lowering. It is also intentionally limited: provider-specific features such as cloud-specific IAM condition keys, proprietary load balancer behaviors, tool-specific macros, or specialized managed services should be handled as extensions or diagnosed as non-portable features rather than treated as part of the portable core.

## Proposed Methodology

The thesis follows a design-science research methodology because it produces and evaluates a technical artifact. The artifact is not only a DSL, but a complete semantic compilation approach for cloud infrastructure.

The methodology consists of six stages:

1. **Domain analysis:** identify the common and partially portable concepts needed to describe cloud architectures across providers and IaC systems.
2. **DSL design:** define the syntax, constructs, validation rules, and examples of a provider-neutral cloud architecture language.
3. **Semantic IR design:** model infrastructure as typed semantic nodes, edges, attributes, constraints, lifecycle information, and portability annotations.
4. **Capability mapping:** define provider/backend profiles that map IR concepts to target resources and classify mappings by fidelity.
5. **Compiler implementation:** build a prototype pipeline from DSL source to AST, IR, capability resolution, backend lowering, and diagnostic reporting.
6. **Evaluation:** test the compiler on benchmark architectures and evaluate portability, semantic fidelity, expressiveness, backend validity, and diagnostic quality.

## Expected Contribution

The expected contribution of this thesis is a compiler-centered framework for semantic cloud portability. Its contribution is not merely that it can produce IaC for more than one platform. The stronger contribution is that it treats cloud infrastructure as a semantic object that can be represented, transformed, analyzed, and diagnosed.

The thesis is expected to contribute:

- a vendor-neutral cloud architecture DSL;
- a semantic IR for infrastructure intent;
- a refined concept taxonomy for portable cloud architectures;
- a provider capability mapping model;
- a prototype compiler that lowers DSL specifications into backend IaC;
- a portability and semantic fidelity diagnostic model;
- a benchmark-based evaluation method for measuring expressiveness and correctness.

Together, these contributions position the work at the intersection of cloud computing, compiler design, model-driven engineering, distributed systems, and programming languages.

## Expected Outcomes

The practical outcome of the thesis is a prototype cloud portability compiler. A user should be able to write one provider-neutral specification and generate backend-specific infrastructure definitions, while receiving a report that explains which parts of the architecture were mapped exactly, partially, through composition, with degradation, or not at all.

The research outcome is a clearer model of semantic portability for cloud infrastructure. The thesis should show that portability is not a binary property. Instead, it can be represented as a set of semantic mappings and evaluated through measurable criteria such as portability score, semantic fidelity score, expressiveness, backend validity, and diagnostic usefulness.

## Scope and Boundaries

This thesis does not claim that all cloud services can be made fully portable. Such a claim would be unrealistic because providers differ in service design, operational guarantees, identity models, networking behavior, pricing structures, regions, compliance features, and lifecycle semantics.

Instead, the thesis takes a more precise research position: common cloud architecture patterns can be represented through a portable semantic core, while provider-specific or weakly portable features should be made explicit through capability mappings and diagnostics. The goal is not to hide cloud differences, but to make them visible, analyzable, and manageable before deployment.

The prototype scope should therefore focus on representative architectures such as static websites, web applications with managed databases, autoscaled API services, private services with internal databases, serverless APIs with storage, and Kubernetes-hosted applications with external services. These cases are sufficient to evaluate whether the DSL and IR can express common infrastructure patterns without depending on provider-specific resources.

## Final Positioning Statement

This thesis positions cloud infrastructure portability as a compiler and semantic modelling problem. It proposes that a unified cloud architecture definition language, supported by a semantic IR and provider capability mappings, can bridge the gap between high-level architectural intent and provider-specific IaC implementation.

The central research contribution is the movement from provider-specific resource description to semantic infrastructure compilation:

```text
Portable cloud intent
  -> Semantic IR
  -> Provider capability mapping
  -> Backend-specific IaC generation
  -> Portability and fidelity diagnostics
```

By making infrastructure intent explicit and by measuring how that intent changes during provider-specific lowering, the thesis aims to provide a more rigorous foundation for multi-cloud deployment, cloud migration analysis, and vendor-neutral infrastructure design.
