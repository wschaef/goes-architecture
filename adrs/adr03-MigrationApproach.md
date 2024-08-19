# ADR Select Migration Approach

**Status** : draft

**Date** : 14.08.2024

**Stakeholders**

- [x] @wschaef

## Context

The challenge is to define the migration approach for the GOES system, considering the following factors:

- **Complexity of the Migration:** The existing system's architecture and dependencies, the number of applications and infrastructure components, and the complexity of interconnections will influence the migration approach.
- **Data Sensitivity and Compliance:** The level of data sensitivity and regulatory compliance requirements will dictate the security and privacy measures needed during the migration process.
- **Business Continuity and Disaster Recovery:** The criticality of the system and the acceptable downtime during migration will determine the need for robust business continuity and disaster recovery plans.
- **Cost and Time Constraints:** The budget allocated for the migration and the desired timeline for completion will influence the choice of migration strategies and tools.
- **Skillset and Expertise:** The availability of internal resources with the necessary skills and expertise to execute the migration will impact the feasibility of different approaches.
- **Cloud Provider Capabilities:** The capabilities and features offered by the chosen cloud provider, including migration tools, support services, and security compliance, will influence the migration strategy.

## Options

1. **Use E-Commerce and Streaming SaaS offerings and migrate the data there**
   - **E-Commerce** (e.g. Shopify, BigCommerce, HPC Commerce), **SaaS Video Streaming White Label Providers** (e.g. VPlayed, Dacast, Brightcove)
     - **Pros:**
       -  Rapid deployment and minimal infrastructure management.
       -  Scalability and flexibility to handle fluctuating demand.
       -  Access to built-in features and functionalities.
     - **Cons:**
       -  Potential vendor lock-in and limited customization.
       -  Data security and privacy concerns.
       -  Cost implications for ongoing subscriptions.
2. **Migrate As-is**
   -  deploy on compute resources of the public cloud
     - **Pros:**
       -  Minimal code changes and quick deployment.
       -  Leveraging existing infrastructure and expertise.
       -  Cost-effective for simple applications.
     - **Cons:**
       -  Limited scalability and flexibility.
       -  Potential performance bottlenecks.
       -  Lack of cloud-native features.
3. **Migrate and improve**
   -  containernize the applications and deploy to the public cloud
     - **Pros:**
       -  Improved portability and scalability.
       -  Enhanced security and isolation.
       -  Access to cloud-native services.
     - **Cons:**
       -  Increased complexity in containerization and deployment.
       -  Potential learning curve for container technologies.
       -  Cost associated with container orchestration tools.
   -  migrate data to databases as service if approriate
     - **Pros:**
       -  Simplified data management and scalability.
       -  Reduced infrastructure costs.
       -  Enhanced performance and reliability.
     - **Cons:**
       -  Potential vendor lock-in.
       -  Data migration challenges and costs.
       -  Security and compliance considerations.
4. **Modernize**
   -  reimplement to be cloud native using higher level services on the public provider
     - **Pros:**
       -  Maximum scalability and flexibility.
       -  Leveraging cloud-native features and services.
       -  Enhanced performance and efficiency.
     - **Cons:**
       -  Significant development effort and cost.
       -  Potential for technical debt and complexity.
       -  Dependency on cloud provider services. 


## Decision

Based on the information provided in document [input1.md](../input1.md), I have made the following assumptions:

- The GOES offering is highly specialized, making SaaS solutions unsuitable.
- The APIs and applications are already containerized.
- Cost and quality are prioritized over strict time constraints.
- The streaming service currently lacks a modern CDN.
- The browser plugin for payment should be replaced with a modern payment solution.
- Native apps for streaming on Android, iOS, and TVs are planned for future development. 

Taking my assumptions and requirements, the option 3 **Migrate and improve** is the best choice for the core GOES application. This is because:

- The existing containerized applications can be easily deployed to the cloud.
- The migration will allow for improved scalability and flexibility to meet future demands.
- The use of cloud-native services will enhance security, performance, and efficiency. 
- The data migration to a database as a service will simplify data management and scalability. 

For the Streaming domain, option 4 **Modernize** is the best choice due to:

- Streaming has the highest load requirements, demanding huge amounts of storage, network bandwidth, and geographical proximity to customers. 

## Concequences

- Data migration will be a significant challenge.
- Modernization of migrated applications towards higher-level services remains an option for the future. 
