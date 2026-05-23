# Title
Design and Implementation of a Unified Cloud Architecture Definition Language : A Semantic Compiler Framework for Portable, Vendor-Neutral Infrastructure-as-Code Generation

## Abstract of Problem Statement: 

Cloud infrastructure provisioning is increasingly difficult due to differences across cloud providers and the reliance on provider-specific tools such as Terraform and AWS CloudFormation. Although Infrastructure-as-Code (IaC) frameworks introduce some level of abstraction, they remain tethered to individual platforms. As a result, migrating systems between providers often requires substantial rework and leads to vendor lock-in.

This Mtech thesis work aims for design and implementation of a vendor-neutral Domain-Specific Language (DSL) for specifying cloud infrastructure. It can be done by creating an Intermediate Representation (IR) that separates user intent from provider-specific implementations. We will also create a prototype compiler to translate DSL specifications into multiple IaC backends while maintaining consistency in behavior across different environments.


## Objectives of this study:

O1: To Design a vendor-agnostic DSL for representing cloud infrastructure intent
O2: To Develop an Intermediate Representation (IR) that represents infrastructure semantics
O3: To design and develop a compiler pipeline to translate DSL to IR to multiple IaC
O4: Define mappings between IR and provider-specific resources
O5: Establish an evaluation framework to assess:

Portability
Semantic fidelity
Expressiveness
