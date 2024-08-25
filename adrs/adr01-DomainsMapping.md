# ADR Mapping of domain to public cloud vs on-premise

## Business Context

**Status** : accepted

**Date** : 13.08.2024

**Stakeholders**

- [x] @wschaef

## Context

GOES System will be deployed in a hybrid cloud environment. This ADR defines which domains of the GOES system will be deployed in the public cloud and which domains will be deployed on-premise.

## Decision Criterias

### Cost

**Public Cloud**: The pay-as-you-go model aligns well with the variable workloads due to peak seasons. Evaluate cloud provider pricing for compute, storage (especially considering the substantial data generated), and data egress fees for streaming content.
**On-Premise**: The existing on-premises infrastructure might offer some cost advantages initially. However, factor in the potential costs of scaling this infrastructure to handle peak loads and the ongoing maintenance expenses.

### Scalability

**Public Cloud**: The ability to rapidly scale resources up or down is crucial for handling the traffic spikes during peak holiday seasons. This elasticity ensures a seamless user experience even during high demand.
**On-Premise**: Scaling the existing infrastructure to handle peak loads might be challenging and expensive. Evaluate the costs and feasibility of procuring and deploying additional hardware within short timeframes.

### Performance

**Public Cloud**: Content streaming demands low latency. Carefully consider the cloud provider's network capabilities and content delivery network (CDN) options to ensure optimal performance for users across different geographical locations.
**On-Premise**: The existing infrastructure might offer good performance for current workloads. However, assess its ability to handle increased traffic during peak seasons and the potential latency implications for global users.

### Security

**Public Cloud**: Sensitive customer data necessitates robust security measures. Evaluate the cloud provider's security features, compliance certifications, and data encryption capabilities. Implement additional security layers as needed.
**On-Premise**: The company likely has existing security protocols in place. Assess their effectiveness in a hybrid environment and ensure they align with cloud security best practices.

### Compliance

**Public Cloud**: The company operates globally, so data privacy regulations like GDPR are critical. Choose a cloud provider that complies with relevant regulations and offers tools to help maintain compliance.
**On-Premise**: Ensure the existing on-premises infrastructure and data handling practices adhere to all relevant data privacy regulations.

### Control

**Public Cloud**: The reduced control over the underlying infrastructure might be a concern. Evaluate the level of control offered by different cloud providers and ensure it meets the company's requirements.
**On-Premise**: The existing on-premises infrastructure offers complete control, which might be beneficial for certain critical workloads or sensitive data.

### Flexibility

**Public Cloud**: The flexibility to experiment with new features and services aligns with the company's innovation mindset. The cloud environment enables rapid provisioning and deprovisioning of resources for testing and development.
**On-Premise**: The existing infrastructure might be less flexible for rapid experimentation and deployment of new services.

### Vendor Lock-in

**Public Cloud**: Carefully evaluate the potential for vendor lock-in and consider strategies to mitigate it, such as using open standards and cloud-agnostic tools.
**On-Premise**: The existing on-premises infrastructure avoids vendor lock-in but might limit access to the latest cloud technologies and services.


## Decision

Following domains will be deployed in the public cloud:

- **Streaming**: This domain requires high scalability and low latency to support global users. The public cloud offers the necessary infrastructure and content delivery network (CDN) capabilities.
- **Shop**: The shop domain experiences significant traffic fluctuations, especially during peak seasons. The public cloud's pay-as-you-go model and elasticity allow for cost-effective scaling.
- **Product**: The product domain benefits from the public cloud's flexibility for rapid experimentation and deployment of new features. 
- **Payment**: The payment domain requires robust security measures and compliance with global regulations. Public cloud providers offer comprehensive security features and compliance certifications.
- **IAM**: The IAM domain can leverage the public cloud's centralized identity and access management capabilities.
- **Ticket System**: The ticket system domain benefits from the public cloud's scalability and availability for handling user requests.
- **SIEM**: The SIEM domain can leverage the public cloud's advanced security analytics and threat detection capabilities.
- **DevOps Plattform**: The DevOps Plattform domain can leverage the public cloud's tools and services for continuous integration and deployment. 

Following domains will be deployed on-premise:

- **Customer**: The customer domain contains sensitive customer data that requires strict control and compliance with data privacy regulations. on-premises deployment allows for greater control over data security and compliance.
- **Order**: The order domain is tightly integrated with the customer domain and requires similar levels of data security and control. on-premises deployment ensures data integrity and compliance. 

## Consequences 
- All dodmains except **Customer** and **Oder** domain have to be migrated to the public cloud.
