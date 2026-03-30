# Cloud Definition Language
> Write infrastructure once. Deploy anywhere.

Design and Implementation of a Unified Cloud Architecture Definition Language. A vendor-neutral DSL for defining cloud architectures and a prototype compiler that can translate such definitions into existing infrastructure-as-code frameworks.

## Problem

Modern Infrastructure as Code (IaC) tools such as Terraform and CloudFormation require users to define infrastructure in a provider-specific and low-level manner. This leads to:

- Lack of portability across cloud providers
- Increased complexity for simple deployments
- Duplication of logic across environments
- Steep learning curve for developers

There is no unified abstraction layer that allows developers to define infrastructure based on *intent* rather than *implementation details*.

## Solution

This thesis introduces a high-level declarative syntax that allows users to define infrastructure using intent-driven configurations.

## Project Status

Under Active Development (M.Tech Thesis Project)

Current Progress:
- [x] Problem Definition
- [ ] DSL Design (Draft)
- [ ] Parser Implementation
- [ ] IR Model
- [ ] AWS Adapter
- [ ] Multi-cloud Support


## License

This project is licensed under the MIT License.


## Author

Tariq Abdullah 
IIT Patna  
