# ADR Select public cloud provider

**Status** : accepted

**Date** : 13.08.2024

**Stakeholders**

- [x] @wschaef

## Context

- GOES is transitioning from a self-managed, on-premises infrastructure to a hybrid cloud environment.
- The company operates globally, handles sensitive customer data, and experiences peak loads during holiday seasons.
- The architecture needs to support scalability, data privacy, compliance, and business continuity.
- The company has an innovation mindset and requires an architecture that can accommodate rapid changes and deployments. 

## Decision

While the final cloud provider selection involves a broader enterprise-level decision encompassing technical, business, and financial requirements, for the purpose of this system design discussion, I'll leverage Google Cloud Platform (GCP) due to my familiarity and experience. The financial aspect of the decision would involve a thorough cost analysis, comparing pricing models, total cost of ownership, and potential for cost optimization across different providers.

I'd like to note that other major cloud providers like AWS or Azure could also be suitable depending on the specific requirements and constraints of the system being designed. The ideal choice would align with both the technical needs and the financial goals of the organization.

## Concequences
- All detailed designs will be done with GCP as cloud provider