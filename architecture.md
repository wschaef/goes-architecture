# Introduction and Goals
This document is intended to be used during a system design interview for a given challenge. It serves as a template to guide the discussion and showcase my understanding of architectural principles and best practices. 

[Input Document](./input1.md) 

This document does not cover all aspects of architecture design, focusing primarily on the requested topics. 
In order to go into details, I had to make assumptions and prioritize certain aspects of the architecture. These assumptions are documented in the "Assumptions" section and will be revisited during the design process. 

## Requirements Overview

### Assumptions:

#### Assumptions on numbers
- **Scope:** E-commerce is fucused on streaming products.
- **User Base:** The company has approximately 50 million active users, with a peak concurrency of 1 million concurrent users. 
- **Peak Load:** Peak load during holiday seasons or special events can result in a 5 times increase in traffic compared to normal conditions.
- **Content Library:** The company has about 10,000 hours of content in its content library of on-demand video and audio content, distributed as follows: 5% SD, 70% HD, and 25% 4K. 
- **Products** The company has about 10,000 products with 1% daily product updates.

#### Assumptions on current architecture
- The current architecture utilizes containers for microservices deployed on-premises.
- GOES company maintains an OpenID Connect compliant Identity Providers (IdP) to authenticate users.
- GOES company maintains other applications for employees on-premise, so that IAM for employees is required on-premise as well.
- GOES consumers already use a 3rd party IdP to authenticate.

The exact numbers for non-functional requirements are assumptions too.

### Functional Requirements:

- **E-commerce Platform:**
    - Maintain the existing feature set from the previous system and extend it with new features. 
- **Content Streaming:**
    - Maintain the existing feature set from the previous system and extend it with new features.
    - Develop native apps for mobile devices and smart TVs.
- **Payment:**
    - Modernize payment processes to align with industry best practices.
    - Eliminate the requirement for browser plugins.
- **Identity and Access Management (IAM):**
    - Implement Single-Sign-On (SSO) across both on-premise and cloud environments. 
- **Hybrid Environment:**
    - Establish reliable data synchronization between the on-premises and cloud environments.
    - Implement secure and high-bandwidth connectivity between the on-premises and cloud environments (e.g., VPN, Direct Connect).
    - Ensure seamless communication and interoperability between applications deployed in both on-premises and cloud environments.
    - Establish end-to-end observability across both environments. 
- **Security:**
    - Implement robust security measures to protect sensitive customer data (e.g., encryption, access control, SIEM).
    - Ensure compliance with relevant data privacy regulations (e.g., GDPR, CCPA).
    - Extend and adopt user roles and permissions from the on-premises system to the hybrid setup.
    - Implement granular access control to cloud resources based on user roles and permissions. 

### Non-Functional Requirements:

*The exact numbers for non-functional requirements are assumptions*

- **Performance:**
    - Maintain system availability of at least 99.95% (less than 4 hours and 23 minutes per year).
    - Ensure an average response time for user requests under 2 seconds under normal load conditions.
    - Achieve low latency streaming with a target latency under 5 seconds for live streams and under 3 seconds for on-demand content.
    - Maintain high video and audio quality across different devices and network conditions, adapting to varying bandwidth availability.
- **Scalability:**
    - Scale horizontally to accommodate a 100% increase in the user base and data volume within 1 month.
    - Dynamically scale bandwidth capacity to meet fluctuating demand, especially during peak hours and special events.
    - Peak load during holiday seasons or special events can result in a 5 times increase in traffic compared to normal conditions.
- **Cost-Effectiveness:**
    - Reduce the total cost of ownership (TCO) of the hybrid cloud environment by at least 15% compared to the current on-premises infrastructure within 3 years.
    - Implement cost optimization strategies for media storage and delivery, such as using different storage tiers based on content popularity and leveraging CDN caching.
- **Security:**
    - Ensure no unauthorized access to sensitive data is detected within a 1-month period.
    - Implement robust authentication and authorization mechanisms to protect user accounts and content.
- **Maintainability:**
    - Deploy new features or updates to the system within 2 weeks.
    - Ensure a mean time to repair (MTTR) of less than 2 hours for critical system failures.
- **Flexibility:**
    - Support the integration of new technologies and services within 3 months.
    - Adapt to new streaming technologies and formats as they emerge, ensuring compatibility with evolving industry standards. 
- **Data recovery**
    - Ensure data recovery time objective (RTO) of less than 4 hours in case of failure.
    - Maintain a data recovery point objective (RPO) of less than 24 hours.

### Additional conciderations

- **Recommendation Engine:** Implementing a recommendation engine could personalize content suggestions for users, potentially improving engagement and satisfaction. This might involve machine learning models and require large datasets for training.
- **Analytics and Monitoring:** Comprehensive analytics and monitoring are crucial for tracking user behavior, content performance, and overall system health. This might involve integrating with cloud-based monitoring tools and potentially developing custom dashboards for real-time insights. 

### Data volume analysis

#### Data domains

- **Customer Data:** This includes personally identifiable information (PII) such as names, addresses, contact details, and payment information. The sensitivity of this data necessitates robust security and compliance measures, potentially influencing the decision to keep some data on-premises in a hybrid model.
- **Transactional Data:** This encompasses data related to purchases, orders, payments, and shipping. The volume and velocity of this data, especially during peak periods like holidays, require a scalable and performant architecture.
- **Product Data:** This includes product descriptions, images, videos, reviews, and inventory information. The need to efficiently store, manage, and deliver this content to a global audience might drive the use of cloud storage and content delivery networks.
- **Streaming Data:** The streaming content aspect of the business generates real-time data related to user viewing habits, content performance, and network traffic. The architecture needs to handle the ingestion, processing, and analysis of this streaming data to gain insights and optimize content delivery.
- **Log and Monitoring Data:** The operation of both on-premises and cloud infrastructure generates logs and metrics related to system performance, security events, and application behavior. The architecture needs to collect, store, and analyze this data for monitoring, troubleshooting, and security purposes. 

#### Data Privacy Conciderations

| Data Domain | Public Cloud Storage | Explanation |
|---|---|---|
| Customer Data |  No | GDPR allows for the transfer of personal data outside the EU, but only under specific conditions. These conditions include ensuring adequate safeguards are in place to protect the data, and the data processor (e.g., the cloud provider) must comply with GDPR regulations. |
| Transactional Data | Partial |  Transactional data may contain information that can be linked back to individuals.  Appropriate anonymization and encryption techniques can be implemented to ensure compliance with GDPR when storing this data on the public cloud. | 
| Product Data | Yes | This data generally does not contain personal information and is less sensitive to GDPR regulations. However, if product reviews contain personally identifiable information, that would need to be considered. |
| Streaming Data | Yes | Streaming data related to user viewing habits can be anonymized or aggregated to avoid direct identification of individuals. This data can be stored in the public cloud, but it's important to ensure appropriate security measures are in place. |
| Log and Monitoring Data | Yes | Logs and metrics related to system performance and security events are generally not considered personal data. However, if these logs contain information that can be linked back to individuals, GDPR compliance needs to be assessed. | 

> **Guideline - Technical reference to user:** A unique, anonymized identifier (e.g., a UUID) can be stored in the public cloud to reference a user. This identifier should only be linked to personal information in the on-premises systems, not in the public cloud. 

The decision about structuring the GOES architecture into domains and choosing the target platform (on-premise or cloud) for each domain is documented here: 
- [ADR Mapping of domain to public cloud vs on-premise](./adrs/adr01-DomainsMapping.md)

## Stakeholders

1. Application Stack developer
2. Enterprise Architect
3. VP Engineering
4. A Business Person of the solution internal to our company

# Architecture Constraints

The current architecture comprises two data centers, one in America and one in Europe, with data replication facilitated by a VPN tunnel. The architecture style adheres to a three-tier model, consisting of web servers, APIs, and databases. 

![Current State Architecture](img/current_state_architecture.png)

# System Scope and Context

## Business Context

### Use Cases: Consumer

1. **Watch Content:** Stream video or audio content on demand or live, including selecting playback quality, subtitles, and audio tracks.
2. **Browse Content:** Discover and explore available content (e.g., movies, TV shows, documentaries, live events) based on genre, popularity, recommendations, or search.
3. **Resume Playback:** Continue watching content from where it was last paused.
4. **Login:** Authenticate user credentials (username/email and password) against the system. 
5. **Create Account:** Register for an account to access personalized features, manage subscriptions, and save watchlists.
6. **Cast to Devices:** Stream content to various devices (e.g., TV, mobile, tablet) using Chromecast or similar technologies.
7. **Purchase Content:** Buy access to premium content, individual episodes, or subscription packages. 
8. **Save Watchlist:** Add content to a watchlist for later viewing.
9. **Manage Profile:** Update personal information, payment details, and subscription plans.
10. **Rate and Review Content:** Provide feedback on content by rating or writing reviews.
11. **Contact Support:** Get assistance with technical issues, account management, or billing. 

### Use Cases: Content Manager

1. **Login** | Authenticate user credentials (username/email and password) against the system.
2. **Upload and Manage Content** | Upload new video and audio content to the system, including metadata (title, description, genre, tags, etc.). 
3. **Edit Content Metadata** | Modify existing content metadata, such as title, description, genre, tags, and release date. 
4. **Organize Content Library** | Create and manage content categories, playlists, and collections to organize the content library. 
5. **Set Content Visibility and Availability** | Control the visibility and availability of content based on region, subscription plans, and other criteria. 
6. **Manage Content Rights** | Assign and manage content rights, including copyright information, licensing agreements, and distribution permissions. 
7. **Monitor Content Performance** | Track content views, engagement metrics (e.g., watch time, completion rate), and user feedback (ratings, reviews). 
8. **Optimize Content Delivery** | Optimize content delivery by adjusting streaming settings, encoding parameters, and CDN configurations. 
9. **Manage User Subscriptions** | Create and manage subscription plans, including pricing, content access levels, and promotional offers. 
10. **Generate Content Reports** | Generate reports on content performance, user engagement, and subscription trends. 

### Use Cases: 1st Level Support

1. **Account Management:** Reset passwords, update account information, manage subscriptions.
2. **Technical Support:** Troubleshoot login issues, streaming problems, device compatibility.
3. **Billing Inquiries:** Address billing questions, payment issues, and subscription changes.
4. **Content Issues:** Report broken links, missing content, or technical problems with content playback.
5. **Escalation:**  Identify and escalate complex issues to higher-level support teams. 

### Use Cases: DevOps

1. **Infrastructure Provisioning:**  Deploy and manage the cloud infrastructure (e.g., virtual machines, storage, networking) required for the system.
2. **Application Deployment:**  Automate the deployment of applications, services, and configurations to the cloud environment.
3. **Monitoring and Logging:**  Set up and maintain monitoring tools to track system performance, identify issues, and generate alerts.
4. **Security Management:** Implement and manage security measures, including access control, vulnerability scanning, and intrusion detection.
5. **Continuous Integration and Delivery (CI/CD):**  Establish CI/CD pipelines to automate code builds, testing, and deployment.
6. **Capacity Planning and Scaling:**  Monitor resource utilization and adjust capacity to meet changing demands.
7. **Troubleshooting and Incident Management:**  Respond to system failures, troubleshoot issues, and restore service.
8. **Cost Optimization:**  Identify and implement strategies to reduce cloud infrastructure costs.

### Use Cases: Security

1. **Monitor Security Events:** Analyze security logs and alerts for suspicious activity.
2. **Investigate Security Incidents:**  Identify the root cause of security incidents and determine the impact.
3. **Tune SIEM Rules:** Adjust SIEM rules to improve detection accuracy and reduce false positives.
4. **Develop Security Reports:** Create reports on security trends, vulnerabilities, and incident response.
5. **Collaborate with Security Teams:**  Work with other security teams to share information and coordinate incident response. 
6. **Maintain SIEM Infrastructure:** Ensure the SIEM system is functioning properly and is up to date. 

## Technical Context

The system context diagram depicts the system's boundaries and its interactions with external entities, including users, external systems, and other relevant stakeholders. It provides a high-level overview of the system's scope and its relationships with the outside world. (following the C4 model level 1) 

<!-- Suggested code may be subject to a license. Learn more: ~LicenseLog:3960891731. -->
![System Context](img/Context.drawio.svg)]

### Persons
- Consumer - A person who uses the system to access content or services.
- Content Manager - A person responsible for creating, editing, and managing the content within the system.
- DevOps - A person or team responsible for the deployment and maintenance of the system's infrastructure and applications.
- Security -  A person or team responsible for ensuring the confidentiality, integrity, and availability of the system.
- 1st Level Support - A person or team providing initial technical support to users of the system.

### External systems
- Content Provider - A third-party service that provides media content to the system.
- Payment Provider - A third-party service that handles financial transactions for the system (e.g., processing payments from consumers).
- external IdP (3rd party IdP) - A third-party service that manages user identities and authentication for the system (e.g., allowing users to log in with their Google, Facebook, or other existing accounts).

# Solution Strategy

The solution strategy involves defining the target architecture, creating a migration path, and adopting the new architecture. 

## Migration approach

### Target Cloud Provider

The decision to choose GCP as the target public cloud provider is documented here: 

- [ADR Select public cloud provider](./adrs/adr02-PublicCloudProvider.md)

### Challenges of Migrating from On-Premise to Hybrid (On-Premise + GCP)

Migrating from a purely on-premise infrastructure to a hybrid model that incorporates Google Cloud Platform (GCP) can present several complex challenges:

**Technical Challenges:**

* **Application Compatibility:** Existing on-premise applications might not be designed for cloud environments, requiring refactoring or rearchitecting to ensure they function correctly and efficiently on GCP.
* **Data Migration Complexity:** Moving large volumes of data to the cloud can be time-consuming and prone to errors. Ensuring data integrity and minimizing downtime during migration are critical considerations.
* **Network Connectivity and Security:** Establishing secure and reliable connectivity between on-premise and cloud environments can be intricate. Configuring firewalls, VPNs, and addressing latency concerns are vital for a smooth hybrid setup.
* **Skill Gaps:** IT team might require upskilling or additional expertise in cloud technologies and GCP-specific tools to manage the hybrid infrastructure effectively.

**Operational Challenges:**

* **Cost Management:** While the cloud offers scalability and potential cost savings, it's crucial to monitor and optimize resource usage to avoid unexpected expenses.
* **Hybrid Management Complexity:** Managing a hybrid infrastructure introduces increased complexity as company needs to coordinate operations and maintain consistency across both on-premise and cloud environments.
* **Change Management:** Migrating to a hybrid model can disrupt existing workflows and processes. Clear communication and change management strategies are essential to ensure a smooth transition for team.
* **Vendor Lock-In Concerns:** While GCP offers numerous benefits, relying heavily on a single cloud provider might create concerns about vendor lock-in and potential future limitations.

**Strategic Challenges:**

* **Defining the Right Hybrid Strategy:** Determining which workloads to migrate to the cloud and which to keep on-premise requires careful planning and alignment with  business goals.
* **Data Governance and Compliance:** Managing data across on-premise and cloud environments requires adherence to data privacy regulations and maintaining strict data governance policies.

Despite these challenges, adopting a hybrid model can bring significant benefits like improved scalability, flexibility, and access to advanced cloud services. Careful planning, addressing these challenges proactively, and selecting the right migration strategy are key to a successful transition to a hybrid on-premise + GCP infrastructure.

### Proposed approach

[ADR Select Migration Approach](./adrs/adr03-MigrationApproach.md) is an Architectural Decision Record (ADR) that explores different approaches to migrating an existing application called GOES to the cloud. It outlines four main options:

- **Rehost**: Lift and shift the application to a virtual machine in the cloud.
- **Replatform**: Move the application to the cloud with minimal changes, potentially leveraging some cloud-native services (like managed databases).
- **Migrate and improve**: Migrate existing containerized applications and improve the architecture using cloud-native services.
- **Modernize**: Completely re-architect the application to be cloud-native, using higher-level cloud services. 

#### 1. Setup the landing zone defining following aspects:

**1. Identity and Access Management (IAM):** 

* **User and Group Management:** Establishing mechanisms to manage user accounts, groups, and their associated permissions within the cloud environment.
* **Authentication and Authorization:** Implementing secure authentication methods (like multi-factor authentication) and defining granular access controls to cloud resources based on roles and responsibilities.
* **Federation and Single Sign-On (SSO):** Integration with existing on-premise identity systems to provide seamless access and unified user management across hybrid environments.

**2. Network Architecture:**

* **Virtual Private Cloud (VPC) and Subnet Design:** Defining the structure and segmentation of the cloud network to ensure isolation and security for different workloads.
* **Connectivity:** Establishing secure connections between on-premise networks and the cloud environment using technologies like VPNs or dedicated interconnects.
* **Load Balancing and Traffic Management:** Implementing load balancers to distribute traffic efficiently across multiple resources and handle potential surges in demand.

**3. Resource Organization and Hierarchy:**

* **Resource Groups and Projects:** Organizing cloud resources into logical groups to streamline management, cost tracking, and access control.
* **Tagging and Labeling:** Applying metadata (tags and labels) to resources to facilitate identification, classification, and automation.
* **Quotas and Limits:** Setting constraints on resource usage to prevent unexpected costs and ensure fair resource allocation across different teams or projects.

**4. Security and Compliance:**

* **Security Policies and Controls:** Implementing security measures like firewalls, intrusion detection systems, and encryption to protect cloud resources and data.
* **Data Protection and Privacy:** Ensuring compliance with data privacy regulations and implementing data encryption, masking, and anonymization techniques where necessary.
* **Audit Logging and Monitoring:** Enabling detailed logging and monitoring of activities within the cloud environment to detect and respond to potential security threats.

**5. Governance and Operations:**

* **Cost Management and Optimization:** Implementing cost tracking, budgeting, and optimization strategies to control cloud spending and maximize resource efficiency.
* **Automation and Orchestration:** Utilizing Infrastructure as Code (IaC) and automation tools to streamline provisioning, configuration, and deployment processes.
* **Disaster Recovery and Business Continuity:** Establishing backup and recovery mechanisms to ensure resilience and minimize downtime in the event of a disruption.

**Additional Considerations:**

* **Landing Zone Blueprint:** Documenting the design and configuration of the cloud landing zone for future reference and scalability.
* **Reference Architectures:** Defining standard architectures and configurations for common workload types to ensure consistency and best practices.
* **Ongoing Optimization and Evolution:** Continuously monitoring, evaluating, and improving the cloud landing zone to adapt to changing business needs and technology advancements.

Some ot theese aspects are designed in **GCP Landing zone - High level view on hybrid cloud setup** chapter

#### 2. Implement a API Gateway MESH

API mesh streamlines cloud migration by decoupling APIs from their implementation, allowing incremental migration without consumer refactoring. It provides dynamic traffic routing for gradual shifts, observability for issue detection, and consistent security across hybrid environments. This ensures a seamless transition with minimal disruption.

#### 3 Adopt DevSecOps platform

A clear and well-defined DevSecOps platform is crucial for teams working in a hybrid environment.  A poor DevSecOps experience can quickly impact the development velocity and overall efficiency of the project. 

Establishing end-to-end monitoring is crucial for a successful migration. It provides valuable insights to guide decision-making throughout the process. Key performance indicators (KPIs) such as stability, load and performance, security and compliance serve as input for determining the migration speed and planning subsequent stages. 

#### 4. Implement a PoC for migration

Choose a component with the fewest dependencies and migrate it to the cloud first. 

#### 5. Migrate Domains

The decision of whether to migrate domains sequentially or in parallel is a crucial one in cloud architecture, impacting project timeline, risk, and resource utilization. Migrating domains sequentially is safer and allows focused troubleshooting, but takes longer. Parallel migration is faster but more complex and risky. The right approach depends on specific environment and needs.

There are two main criteria to prioritize domains for migration:
- **Scalability Capacity:**  Assess the potential for each domain to benefit from cloud scaling capabilities, considering factors like peak load, user growth, and data volume.
- **Potential Cost Savings:**  Evaluate the cost-effectiveness of migrating each domain, factoring in cloud resource pricing, infrastructure maintenance, and potential operational efficiency gains.

Streaming domain is a good candidate, because of highest load and highest potential cost savings.

Scalability of the remaining domains on-premise will be improved because migrating domains to the cloud frees up resources on-premise. This allows for a more efficient use of the remaining on-premise resources, potentially enabling better performance for the remaining domains. 

### Migration of data

Google Cloud Platform (GCP) provides a suite of features designed to streamline the conversion and transfer of large datasets from on-premise environments, even enabling database transformations during migration: 

**Data Transfer and Conversion Tools**

* **Storage Transfer Service:**  Handles bulk data transfers from on-premise storage (including NFS) to Cloud Storage, efficiently moving terabytes or petabytes of data. 
* **Database Migration Service:** Facilitates seamless database migrations, supporting schema and data type conversions to ensure compatibility with GCP databases like Cloud SQL or Spanner.  
* **Dataflow:** A fully-managed data processing service, ideal for transforming data during migration, enabling format changes, cleansing, or enrichment.

# Building Block View

## Whitebox Overall System

Motivation  
*The whitebox view of the overall system provides a detailed breakdown of the system's internal components and their interactions. This view is essential for understanding the system's architecture in depth, identifying potential dependencies, and analyzing the flow of data and control within the system. It helps developers, architects, and other stakeholders understand how the system works at a granular level, enabling them to make informed decisions about design, implementation, and maintenance.* This view corresponds to C4 model level 2. 

![Whitebox View of Overall System](img/Container.drawio.svg)

- **Streaming:** This building block is responsible for the delivery of video and audio content to consumers. It handles tasks like encoding, transcoding, and streaming protocols. 
- **Shop:** The Shop building block represents the frontend for e-commerce aspects of the system.
- **Order:** This building block manages the order process, including order creation, fulfillment, and tracking.  
- **Product:** This building block stores and manages product information, including metadata, pricing, and inventory.
- **Customer:** The Customer building block stores and manages user information, including account details. 
- **Payment:** This building block handles financial transactions, including payment processing and fraud detection. 
- **IAM *(Identity and Access Management)*:** The Identity building block manages user authentication and authorization, allowing users to log in and access system resources. 
- **Ticket System:** This building block provides a system for users to report issues and request support. 
- **DevSecOps Platform:** The DevSecOps Platform provides a unified environment for managing infrastructure, deploying applications, and integrating security measures throughout the software development lifecycle. 
 
## Landing zone - High level view on hybrid cloud setup

### Considerations

### VPC, Regions, Zones

* **Single VPC:** A single VPC will be created in GCP to ensure centralized network management and control for all cloud resources.
* **Two Regions:** 
    * **US Region:** A region on the US East Coast, geographically close to the existing US on-premise data center. (Example: `us-east1`)
    * **Europe Region:** A region in Western Europe, geographically close to the existing European on-premise data center. (Example: `europe-west1`)
    * **Optional:** Additional regions can be considered for future expansion or specific business needs.
* **Zones:** Each region will have at least two zones for high availability and fault tolerance.

### Hybrid Setup

#### Network Setup

* **High-Level View:** The network setup will focus on establishing secure and performant connectivity between the on-premise data centers and the GCP VPC, while also providing efficient traffic routing and content delivery.

* **Cloud Interconnect:** 
    * **Dedicated Interconnect:** Dedicated Interconnect will be used in each region to provide stable and high-bandwidth (10Gbps or 100Gbps) connectivity between the on-premise data centers and the GCP VPC. This ensures low latency and high throughput for critical workloads.
    * **Redundancy:** Multiple Dedicated Interconnect connections can be established for redundancy and increased availability.

* **Subnet Design:**
    * **Public Subnets:** Public subnets will be created in each region to host internet-facing resources such as web servers, load balancers, and Cloud CDN edge nodes.
    * **Private Subnets:** Private subnets will be created in each region to host backend services like APIs, databases, and application servers. These subnets will not have direct internet access, enhancing security.

* **Load Balancing:**
    * **Cloud DNS (Geolocation-based):** Cloud DNS will be the primary mechanism for distributing traffic across regions based on the user's geographical location.
    * **Regional Load Balancers:** Regional Load Balancers (e.g., Internal TCP/UDP Load Balancer, Network Load Balancer) will be used within each region to distribute traffic across instances within that region once Cloud DNS has directed the user there.

* **Cloud DNS:** 
    * **External DNS:** Cloud DNS will manage external DNS records, allowing users to access resources using custom domain names. 
    * **Internal DNS:** Cloud DNS can also be used for internal DNS resolution within the VPC, enabling seamless communication between resources across different subnets and regions.

* **Cloud CDN:** Cloud CDN will be used to cache static and dynamic content at edge locations around the world, reducing latency and improving performance for end users.

#### Data recovery

In a hybrid setup with stringent RTO (<4 hours) and RPO (<24 hours) requirements, data recovery on the GCP side warrants careful attention to the following strategies, tailored to the specific services in use - Cloud SQL, Firestore, Cloud Storage, and Media CDN:

Cloud SQL

* **Automated backups:** Ensure that automated backups are enabled with a frequency that aligns with the RPO, potentially hourly or even more frequently.
* **Regional replicas:** Consider deploying replicas in a different region within the same multi-region VPC to enable faster failover and recovery should a regional outage occur.
* **Point-in-time recovery:** Leverage this feature to restore the database to a specific point in time within the backup retention period. 

Firestore

* **Multi-region deployment:** Opt for a multi-region deployment of Firestore to achieve high availability and automatic failover in the event of regional disruptions. 
* **Data exports:** Regularly export Firestore data to Cloud Storage to create additional backup and recovery options.
* **Point-in-time recovery:** Utilize the built-in point-in-time recovery feature to restore data to a specific point in the past. 

Cloud Storage

* **Object Versioning:** Enabling versioning is recommended to preserve previous versions of objects, providing a safety net against accidental deletions or overwrites.
* **Cross-region replication:** Consider replicating critical data to a bucket in a different region for added durability and faster recovery in case of a regional outage.
* **Lifecycle management:** Implementing lifecycle policies can automatically transition objects to lower-cost storage classes (like Coldline or Archive) for long-term retention and cost optimization.

Media CDN

* **Origin redundancy:** Configuring multiple origins (such as Cloud Storage buckets in different regions) helps ensure content availability even if one origin becomes unavailable.
* **Cache invalidation:** Implement mechanisms to invalidate cache content quickly in case of updates or errors to ensure users receive the latest versions. 

Additional Considerations

* **Monitoring and Alerting:** Comprehensive monitoring and alerting should be implemented to promptly detect failures or performance degradation in any of the GCP services.
* **Automated Recovery:** Develop and test automated recovery workflows to minimize manual intervention and ensure timely recovery within the RTO. 
* **Regular Testing:** Periodic disaster recovery drills are vital to validate the effectiveness of the recovery strategies and identify areas for improvement. 

By harnessing the inherent capabilities of these GCP services and implementing robust recovery mechanisms, organizations can achieve the desired RTO and RPO even in the face of unexpected disruptions. Continuous monitoring, testing, and refinement of the recovery strategy are crucial to ensuring data protection and business continuity in the hybrid environment. 


#### API Gateway Mesh

* **Apigee Hybrid:** Apigee Hybrid will be used to deploy API Gateway instances both on-premise and in GCP.
* **Mesh Setup:** Apigee Hybrid's mesh functionality will establish secure communication and unified management across the on-premise and cloud API Gateway instances. 
* **Benefits:** This hybrid mesh setup provides flexibility in API deployment, allowing for gradual migration of APIs from on-premise to the cloud while maintaining a consistent experience for API consumers.

#### Security

* **Cloud Armor:** Cloud Armor will be deployed in front of the internet-facing resources in the public subnets to protect against DDoS attacks, SQL injection, cross-site scripting (XSS), and other common web application vulnerabilities. 
* **Identity-Aware Proxy (IAP):** IAP will be used to control access to internal applications and resources hosted in the private subnets. IAP enables fine-grained access control based on user identity and context, eliminating the need for a traditional VPN.
* **Virtual Private Cloud (VPC) Firewall:** VPC Firewall rules will be configured to control traffic flow between different subnets and resources within the VPC. 
* **Identity and Access Management (IAM):** IAM will be used to manage user access and permissions to GCP resources. 
* **Security Command Center:** Security Command Center can be used for centralized security monitoring, threat detection, and compliance management across the hybrid environment.

* **Security Groups:** Security groups can provide additional layer of network security by controlling traffic at the instance level.
* **Data Encryption:** Data at rest and in transit should be encrypted using appropriate mechanisms.

#### Non-Prod Environments 

Non-prod environments are not in scope for this document. However, from a high-level perspective, non-prod environments can be set up similarly to the production environment, with the following key distinctions:

- **Resource Allocation:**  Non-prod environments will typically have a smaller allocation of resources (e.g., compute instances, storage) compared to the production environment.
- **Isolation:**  Non-prod environments should be isolated from the production environment to prevent accidental data corruption or security breaches. This can be achieved through network segmentation, separate databases, or other isolation mechanisms. 
- **Access Control:**  Access to resources in non-prod environments should be less restrictive than in the production environment, allowing for direct access by DevOps for development and testing purposes. This ensures flexibility and agility during the development lifecycle. 

### Design

![High level view on hybrid cloud setup](img/LandingZone.drawio.svg) 

## Domain Streaming

### Considerations

1.  **Video Encoding and Transcoding:**
    - **Complexity and Resource Intensiveness**: Video encoding and transcoding require substantial computational resources, especially for high-resolution and high-bitrate content. Maintaining real-time encoding and transcoding while ensuring optimal quality across various devices and network conditions can be challenging.
    - **Codec Selection and Optimization**: Choosing the right video codecs (e.g., H.264, H.265, AV1) and optimizing their parameters to balance compression efficiency, quality, and compatibility across different platforms is crucial.
2.  **Content Delivery Network (CDN):**
    - **Global Scalability**: Handling massive concurrent user requests and delivering content with minimal latency to a geographically distributed audience requires a robust CDN. Scaling the CDN infrastructure to accommodate traffic spikes and maintaining consistent performance across regions can be technically complex.
    - **Adaptive Bitrate Streaming (ABR)**: Implementing ABR to dynamically adjust video quality based on network conditions and user device capabilities adds complexity to content delivery. Efficiently segmenting videos into multiple bitrate representations and seamlessly switching between them during playback is a technical hurdle.
3.  **Video Player Development:**
    - **Cross-Platform Compatibility**: Building a video player that works flawlessly across various operating systems, browsers, and devices (desktops, smartphones, tablets, smart TVs) presents significant challenges. Ensuring consistent playback experience and handling platform-specific nuances can be complex.
    - **Low-Latency Playback**: Achieving low-latency playback, especially for live streaming events, requires careful optimization of the video player, streaming protocols, and network infrastructure. Minimizing buffering and ensuring smooth playback even with fluctuations in network conditions can be demanding.
4.  **Digital Rights Management (DRM):**
    - **Content Protection**: Implementing robust DRM solutions to prevent unauthorized access, copying, and distribution of content is essential. Integrating DRM technologies securely into the streaming platform while maintaining a smooth user experience can be technically challenging.
    - **License Management**: Managing DRM licenses, including issuing, renewing, and revoking them, requires a complex backend system. Ensuring efficient license management and handling various DRM ecosystems adds technical overhead.
5.  **User Management and Authentication:**
    - **Secure Authentication**: Implementing secure user authentication and authorization mechanisms to protect user accounts and prevent unauthorized access is crucial. Handling password management, multi-factor authentication, and integration with third-party identity providers can be technically involved.
    - **Subscription Management**: Managing user subscriptions, including plan upgrades, downgrades, cancellations, and payment processing, requires a robust backend system. Handling various subscription models and integrating with payment gateways adds technical complexity.
6.  **Analytics and Monitoring:**
    - **Real-time Monitoring**: Implementing real-time monitoring and analytics to track user engagement, playback quality, and system performance is crucial. Collecting, processing, and analyzing massive amounts of data in real time presents technical challenges.
    - **Quality of Experience (QoE) Measurement**: Accurately measuring and optimizing QoE, including factors like buffering, startup time, and video quality, across diverse user environments and network conditions requires sophisticated analytics tools and techniques.

### Data volume estimation

**Relevant Assumptions:**
- **Content Library:** The company has about 10,000 hours of content in its content library of on-demand video and audio content, distributed as follows: 5% SD, 70% HD, and 25% 4K. Avarage bitrates - SD: 2 Mbps, HD: 5 Mbps, 4K: 15 Mbps -> 7,35 Mbps avarage bitrate

**Data Volume (net):**  35 TB
> 7,35 Mbps * 10000 hours * 3600 sec/hour : 8 bits/byte : 1.000.000 MB/TB = 33,075 TB

**Data Volume (gross):** 150TB
> net * 2 regions * 2 for ABR = 132,3 TB

### Design

Some aspects are omitted in this diagram but can be reviewed in the description of the landing zone. 

For a scalable and resilient streaming platform, videos are initially uploaded to **Cloud Storage**.  A **Pub/Sub** message triggers the **Transcoder API** to process the video into multiple formats and resolutions optimized for various devices. **Cloud Functions** manage the orchestration, handling tasks like updating metadata and triggering CDN cache invalidation. The processed videos are stored back in **Cloud Storage** and distributed globally via **Media CDN** for low-latency delivery. **Cloud Armor** acts as a protective layer, safeguarding against DDoS attacks and other threats, ensuring uninterrupted streaming experiences for users. 

The **Content Management Application** serves as a user interface and data management tool, storing and retrieving structured data related to content management within **Cloud SQL**. This data might include user information, content categories, workflows, publishing schedules, and other organizational elements. The application itself does not handle the storage or processing of actual content or its metadata. 

The **Streaming Application** is a multi platform frontend for consumers.

> **Media CDN** fulfills all requirements on streaming 
> **Cloud Functions** and **Trancoder API** is a very cost effective solution

![Domain Streaming](img/DomainStreaming.drawio.svg)

## Domain Customer

### Considerations

Domain Customer remains on premise and is interated with other domains by REST APIs.

### Data volume and network bandwidth estimation

**Relevant Assumptions:**
- **User Base:** The company has approximately 50 million active users, with a peak concurrency of 1 million concurrent users. 
- **Peak Load:** Peak load during holiday seasons or special events can result in a 5 times increase in traffic compared to normal conditions.
- **Record Size:** 1KB per user for Personally Identifiable Information (PII)
- **Change Rate:** 0,01% of users change their PII per day
- **Read Rate:** 100 requests per hour per active user (caching of PII data is a complicated topic)

**Data Volume (net):**  50GB
> 1KB * 50 million users = 50GB
**Data Volume (gross):** 200GB
> net * 2 regions * 2 zones  = 200GB
**Data Volume (Read):** 140MB/sec
> 1KB * 1 million active users * 100 requests/hour : 3600 seconds/hour = 139MB/sec
**Data Volume (Write):** 1KB/sec
> 1KB * 50 million users * 0,01% : 86.400 seconds/day = 1KB/sec

**Network bandwidth inter cloud**: 4,5 Gbps
> 140 MB/sec * 8 bit/byte = 1120 Mbps
> 1120 Mbps * 2 (daily distribution) + 20% Overhead = 2688 Mbps
> 2688 Mbps : 3 (compression factor) = 896 Mbps
> 895 Mbps * 5 peak load = 4500 Mbps

### Design

Some aspects are omitted in this diagram but can be reviewed in the description of the landing zone. 

![Domain Customer](img/DomainCustomer.drawio.svg) 

## Domain Order

### Considerations

Domain Order remains on premise and is interated with other domains by REST APIs.

### Data volume and network bandwidth estimation

**Relevant Assumptions:**
- **User Base:** The company has approximately 50 million active users, with a peak concurrency of 1 million concurrent users. 
- **Peak Load:** Peak load during holiday seasons or special events can result in a 5 times increase in traffic compared to normal conditions.
- **Record Size:** 1KB per order
- **Change Rate:** 10% of active users make an order transaction per day
- **Read Rate:** 10% of the change rate
- **Retention Period:** 10 years

**Data Volume (net):** 20TB
> 1KB * 50 million users * 10% = 5GB #data per day
> 5GB * 365 days * 10 years = 18250 GB
**Data Volume (gross):** 80TB
> net * 2 regions * 2 zones  = 80TB
**Data Volume (Read):** 6KB/sec
> 60KB/s * 10% = 6KB/sec
**Data Volume (Write):** 60KB/sec
> 5GB/day : 86.400 seconds/day = 60KB/sec

**Network bandwidth inter cloud**: 2,5 Mbps
> 66 KB/sec * 8 bit/byte = 528 kbps -> 600 kbps
> 600 kbps * 2 (daily distribution) + 20% Overhead = 1440 kbps -> 1,5 Mbps
> 1,5 Mbps : 3 (compression factor) = 0,5 Mbps
> 0,5 Mbps * 5 peak load = 2,5 Mbps

### Design

Some aspects are omitted in this diagram but can be reviewed in the description of the landing zone. 

![Domain Order](img/DomainOrder.drawio.svg) 

## Domain Products

### Considerations

#### Compute

**Assumptions**
- The current architecture utilizes containers for microservices deployed on-premises.

**Potential Migration Options**
- Google Kubernetes Engine (GKE)
- Cloud Run

**Chosen Solution: Cloud Run**

Cloud Run's alignment with the objectives of simplifying operations, improving scalability, and reducing costs makes it the preferred solution for migration. 

* **Simplified Operations:** Cloud Run's fully managed environment and serverless execution model eliminate the need for infrastructure management, allowing for a focus on application development and faster deployment cycles. 
* **Improved Scalability**: The automatic scaling capabilities of Cloud Run ensure optimal resource utilization and cost-effectiveness, particularly in scenarios with fluctuating workloads.
* **Reduced Costs**: The pay-per-use pricing model further contributes to cost optimization compared to the fixed costs associated with maintaining on-premises infrastructure or running a dedicated Kubernetes cluster.

While GKE offers greater flexibility and control, it also introduces additional operational complexity. Cloud Run's streamlined approach is considered more suitable for the current requirements, where the priority is on achieving operational efficiency and cost savings without sacrificing the benefits of containerization.

#### Database

**Relevant Assumptions**:
- **User Base:** The company has approximately 50 million active users, with a peak concurrency of 1 million concurrent users. 
- **Products:** The company has about 10,000 products with 1% daily product updates.
- **Record Size:** 10KB text description, 1MB images, 10MB videos per product
- **Change Rate:** 1% daily product updates -> 100 product updates per day
- **Read Rate:** 20 product views per user per day for 50 millions users
- **Peak Load:** Peak load during holiday seasons or special events can result in a 5 times increase in traffic compared to normal conditions.

There are different types of data, such as media and metadata, which require different databases.
Considering the requirement to scale five times and the need for replication for availability, the Products domain must meet the following data requirements: 

| Data Type | Data Volume Store | Data Volume Write per Day | Data Volume Read per Day | Comment |
|---|---|---|---|---|
| Meta data | 200MB | 5MB | 50TB | 10KB text description * 10000 products * 2 replication, 10kb * 100 updates * 5 scale, 10KB * 20 views * 50 million active users * 5 scale |
| Media data | 220GB | 5,5GB | 55PB | 11BM * 10000 products * 2 replication, 11MB * 100 updates * 5 scale, 11MB * 20 views * 50 million active users * 5 scale |

##### Meta Data 

**Elastic Cloud** is selected to fulfill the following requirements:

* **Data Storage and Management:**
    * Data Type: Product metadata
    * Data Volume Store: 200MB
    * Data Volume Write per Day: 5MB
    * Data Volume Read per Day: 50TB

* **User Experience:**
    * Advanced product search capabilities, including:
        * Fuzzy search
        * AI-powered search
        * Autocomplete
    * Personalized product recommendations

**Rationale:**

Elasticsearch, while powerful for search, can be complex to deploy and scale, requiring dedicated technical expertise. Elastic Cloud simplifies this by providing a managed service, allowing to focus on leveraging Elasticsearch's advanced search capabilities, like full-text search, faceted navigation, and relevant result ranking, without the operational overhead.

**Additional Benefits:**

* Scalability: Elastic Cloud can easily scale to accommodate growing data volumes and user traffic.
* Managed Service: Reduces operational overhead and eliminates the need for in-house Elasticsearch expertise.
* High Availability: Ensures reliable access to product data and search functionality.

**Implementation Notes:**

* Product metadata will be indexed and stored in Elasticsearch indices within Elastic Cloud.
* Search and recommendation APIs will be utilized to power the product search and recommendation features in the application.

**Concequences**

- The microservices will need to be adapted to use Elasticsearch SQL, as it offers a limited feature set. 

##### Media Data

Based on the significantly larger data volume and the unstructured nature of media data (videos and images), Cloud Storage emerges as the most appropriate solution on GCP.

- **Key advantages of Cloud Storage:**
    - **Massive Scalability:** Cloud Storage is designed to handle petabytes or even exabytes of data, making it well-suited for storing and serving vast amounts of media content.
    - **Object Storage:** Media files are typically stored as objects, and Cloud Storage provides a simple and scalable object storage interface, aligning perfectly with this requirement.
    - **High Performance:** Cloud Storage offers various storage classes with different performance characteristics, allowing for a balance between cost and performance based on specific access patterns.
    - **Global Accessibility:** Media content often needs to be accessed from various locations, and Cloud Storage's global network ensures fast access to media data from anywhere in the world.
    - **Integration with other GCP services:** Cloud Storage integrates seamlessly with other GCP services like Cloud CDN for content delivery, Cloud Vision API for image analysis, and Transcoder API for video processing. This allows for building a complete media management and delivery pipeline on GCP.
- **Additional Considerations:**
    - **Storage Classes:** Choosing the appropriate storage class (Standard, Nearline, Coldline, Archive) based on access patterns and cost considerations is crucial.
    - **Data Organization:** Organizing media data using buckets and object names ensures efficient retrieval and management.
    - **Content Delivery:** Consider using Cloud CDN to optimize the delivery of media content to users around the world. 

#### Multi Region aspects

Cloud Storage, and Elastic Cloud provide out-of-the-box multi-region setup. For deployment in a multi-region setup, follow the best practices recommended by these services. 

#### Integration with domain Order

Domain Order remains on premise and is intergated with other domains by REST APIs. The challenge there is to prevent customer data leakage into public cloud. 
- **Elastic Cloud supports several mechanisms to obfuscate data privacy-relevant fields:**
    - **Field-Level Security:** Restricts access to sensitive fields based on user roles and permissions.
    - **Data Masking and Anonymization:** Transforms sensitive data before indexing, using techniques like hashing or encryption.
    - **Pseudonymization:** Replaces identifying information with artificial identifiers to enable analysis while preserving privacy.
    - **Aggregation and Reporting:** Allows deriving insights from data without exposing individual records or sensitive fields. 

### Design

Some aspects are omitted in this diagram but can be reviewed in the description of the landing zone. 

![Domain Product](img/DomainProduct.drawio.svg) 

## Domain Shop

### Considerations

#### Compute

**Assumptions**
- The current architecture utilizes containers for microservices deployed on-premises.

**Potential Migration Options**
- Google Kubernetes Engine (GKE)
- Cloud Run

**Chosen Solution: Cloud Run**

Cloud Run's alignment with the objectives of simplifying operations, improving scalability, and reducing costs makes it the preferred solution for migration. 

Domain Shop provides two web applications:
- Shop for Consumers
- Shop for Content Managers

The reason for separating them is fundamentally different authorization requirements. 

Both applications use Memorystore to maintain user session e.g tokens and cache.

Business logic is implemented in the Shop API which uses Firestore to maintain user profiles. This Shop API is used by Streaming Domain to manage subscriptions and purchases. 

#### Database

**Relevant Assumptions**:
- **User Base:** The company has approximately 50 million active users, with a peak concurrency of 1 million concurrent users. 
- **Record Size:** 10KB user profile size
- **Change Rate:** 1% of users update their profile daily
- **Read Rate:** One user profile read per user session 
- **Peak Load:** Peak load during holiday seasons or special events can result in a 5 times increase in traffic compared to normal conditions.

| Data Domain | Data Volume Store | Data Volume Write per Day | Data Volume Read per Day | Comment |
|---|---|---|---|---|
| User Profile | 1TB | 25GB | 2,5TB | 10KB user profile size * 50 million users * 2 replication, 10KB * 50 million users * 1% * 5 scale | 10KB * 50 million users * 5 scale | 

Based on the provided data, Firestore appears capable of handling the projected requirements. It comfortably accommodates the estimated 1TB of user profile data and the 25GB of daily writes. While the estimated 2.5TB of daily reads might approach Firestore's limits under peak usage or with complex queries, the platform's automatic scaling should generally manage this load. Additionally, Firestore's design inherently supports high levels of concurrency, suggesting it can handle the anticipated 1 million concurrent users, provided the data model and queries are optimized. 

### Multi Region aspects

Firestore provides out-of-the-box multi-region setup. For deployment in a multi-region setup, follow the best practices recommended by these services. 

### Design

Some aspects are omitted in this diagram but can be reviewed in the description of the landing zone. 

![Domain Shop](img/DomainShop.drawio.svg) 

## Domain Payment

### Considerations

Payment is already separated from the system. Integration via browser plug-in is an issue, as it creates an unnecessary barrier for user experience.

There are two options for proceeding with Payment:

- Improve and replace the browser plug-in integration using modern browser features.
- Migrate Payment to GCP using Payment Gateway.

Decision:
Delay the migration to GCP because the current payment provider offers a mature and stable solution, and migrating to a new provider would require significant effort and risk, potentially disrupting the user experience. 

## Domain Ticket System

### Considerations

**Assumption:** The GOES system currently utilizes an on-premises ticketing system.

**Recommendation:** Migrate to a SaaS ticketing provider. 

In the well-established realm of ticketing systems, SaaS solutions present a compelling alternative to traditional on-premise deployments, offering key advantages that align with modern business needs. 

**Key Benefits of SaaS Ticketing:**

* **Scalability & Agility:**  SaaS effortlessly accommodates growth and fluctuating demand, eliminating on-premise performance limitations.
* **Reduced IT Overhead:** Offload maintenance, upgrades, and security to the provider, allowing IT to focus on strategic initiatives.
* **Enhanced Accessibility & Collaboration:** Empower teams to provide support from anywhere, fostering seamless collaboration and responsiveness.
* **Cost Predictability:**  Transparent subscription models enable better financial planning and eliminate unexpected infrastructure costs.

**Leading SaaS Ticketing Providers:**

* **Zendesk:** Comprehensive customer service platform with ticketing, live chat, and knowledge base. 
* **Freshdesk:** User-friendly helpdesk solution with automation and collaboration features. 
* **Jira Service Management:**  Extends Jira's capabilities for IT service management (ITSM) and customer support.
* **ServiceNow:** Enterprise-grade platform for IT, customer, and employee service management.
* **Zoho Desk:** Cost-effective helpdesk solution with integration into the Zoho ecosystem.

## Domain IAM

### Considerations

#### Assumptions
- GOES company maintains an OpenID Connect compliant Identity Providers (IdP) to authenticate users.
- GOES company maintains other applications for employees on-premise, so that IAM for employees is required on-premise as well.
- GOES consumers already use a 3rd party IdP to authenticate.

#### Migration vs On-premise
An OpenID Connect compliant Identity Provider is designed to be used outside of an intranet. The deployment of such an IdP is transparent to the workloads as long as the OAuth2 endpoints of the IdP are available. Based on the assumptions, there is no need for migration. 
If scalability becomes an issue, then the B2C IdP can be migrated to GCP.
B2B (=partners) and B2E (=employees) will remain on-premise. 

#### Zero Trust
One of the pillars of the Zero Trust approach is to validate the user identity at every component in the infrastructure. On GCP, Identity-Aware Proxy provides a service that can intercept requests to workloads deployed on GKE or Cloud Run and validate the user identity. 
M2M authentication and authorization can be enforced at the API Gateway. 

#### Integrating On-Premise IAM with GCP IAM 

The core goal here is to establish a unified identity and access management system across hybrid environment, allowing users to seamlessly access resources in both on-premise infrastructure and GCP with their existing credentials. Here's how we can approach it:

**1. Identity Federation:**

* **Establish trust:** Set up a trust relationship between on-premise identity provider (IdP) and GCP. This allows GCP to rely on IdP for user authentication.
* **Protocols:** Choose a suitable federation protocol like SAML (Security Assertion Markup Language) or OpenID Connect (OIDC) to enable secure communication and authentication exchange between the two systems.
* **User attributes:** Map relevant user attributes (e.g., username, email, group memberships) from on-premise IdP to GCP IAM roles and permissions.

**2. GCP IAM Roles and Permissions:**

* **Role-based access control (RBAC):** Define granular IAM roles in GCP that align with on-premise roles and responsibilities. Assign appropriate permissions to each role, granting access to specific GCP resources and services.
* **Group synchronization (optional):** If IdP supports it, consider synchronizing on-premise groups with GCP groups to simplify user management and role assignments.

**3. Organization, Folder, and Project Structure:**

* **Organization:** This is the root node in the GCP resource hierarchy. It represents company or entity and allows centralized management and policy enforcement across all GCP resources.
* **Folders:** Folders provide an additional layer of organization within the hierarchy. They can be used to group projects based on departments, business units, or any other logical categorization.
* **Projects:** Projects are the basic units of organization in GCP. They contain the actual cloud resources (e.g., virtual machines, storage buckets, databases) and provide isolation and access control boundaries.

**Key Considerations:**

* **User provisioning:** Determine how new users will be provisioned in GCP (e.g., automatically upon first login or through a manual process).
* **Group management:** Decide how on-premise group changes will be reflected in GCP (e.g., real-time synchronization or periodic updates).
* **Policy inheritance:** Leverage policy inheritance to apply organization-level policies to folders and projects, simplifying management and ensuring consistency.
* **Access reviews:** Regularly review and audit access rights to ensure least privilege and prevent unauthorized access.

### Design

Some aspects are omitted in this diagram but can be reviewed in the description of the landing zone. 

![Domain IAM](img/DomainIAM.drawio.svg) 

### DevSecOps Platform

#### Monitoring

**Problem Statement:**

Organizations with hybrid infrastructure face challenges in achieving unified monitoring and security due to the disparate nature of on-premise and cloud environments. This fragmentation hinders comprehensive visibility, impacting troubleshooting, capacity planning, proactive issue identification, and threat detection.

Elastic Cloud offers a compelling solution by centralizing monitoring and security data from both environments, providing a single pane of glass for observability and threat detection. Its scalability, high availability, and managed service model ensure efficient monitoring and security that keeps pace with infrastructure growth. Seamless GCP integration further enhances its value in hybrid setups, enabling organizations to gain insights, troubleshoot, optimize performance, and detect and respond to security threats across their entire infrastructure. 

Additionally, Elastic Cloud's security monitoring capabilities empower DevSecOps teams to proactively identify and address vulnerabilities, enhancing the overall security posture of the hybrid environment.

![Monitoring](img/DomainDevSecOps.drawio.svg)

#### Development Platform

There are three options for proceeding with the current Developer Platform: 

**1. Improve current Developer Platform to work seamlessly with GCP**

This option requires less effort to adopt, but its suitability depends on the current maturity of the existing Developer Platform. 

**2. Migrate to Google Cloud Developer Platform**

Google Cloud provides a comprehensive developer platform that empowers developers to build, deploy, and manage applications and services on Google's scalable infrastructure. It offers a wide array of tools and services tailored to various development needs, including:

- **Cloud Code**: An integrated development environment (IDE) plugin that simplifies the development and deployment of applications to Google Cloud.
- **Cloud Shell**: A browser-based shell environment for managing Google Cloud resources and running command-line tools.
- **Cloud Build**: A fully managed continuous integration and continuous delivery (CI/CD) platform for automating build, test, and deployment processes.
- **Cloud Deploy**: A managed service for deploying applications to Google Kubernetes Engine (GKE) or Cloud Run.
- **Artifact Registry**: A fully managed artifact repository for storing and managing build artifacts, such as container images and language packages. 
  
This option offers the best integration with GCP services, but requires additional manual effort to ensure compatibility with the on-premise environment. 

**3. Migrate to an alternative**

Here are the top 5 Developer Platforms, ranked by their suitability for a hybrid setup: 

| Rank | Platform | Strengths | Considerations |
|---|---|---|---|
| 1 | GitLab | **Flexibility:** Handles diverse deployment targets on GCP and on-premise.<br>**Unified DevOps:** Offers a complete tool suite within one platform.<br>**Hybrid Cloud Support:** Features enable efficient management of hybrid environments.<br>**Elastic Integration:** Can be seamlessly integrated with Elastic Cloud for observability and security. | **Self-Hosting:** On-premise setup might require additional maintenance and expertise. |
| 2 | Azure DevOps | **Strong CI/CD:** Robust pipeline capabilities for reliable deployments.<br>**Azure Integration:** Works seamlessly with Azure services.<br>**Hybrid Support:** Azure Arc facilitates on-premise resource management.<br>**Elastic Integration:** Can be integrated with Elastic Cloud, but might require additional configuration. | **Configuration:** More setup for on-premise deployments compared to GitLab.<br>**Microsoft Ecosystem:** Deeper integration with Microsoft tools. |
| 3 | Jenkins | **Flexibility & Customization:** Open-source nature allows extensive tailoring to hybrid setup and toolchain.<br>**Wide Integration:** Numerous plugins available, including for Elastic Cloud.<br>**Elastic Integration:** Can be easily integrated with Elastic Cloud for centralized logging and monitoring. | **Manual Setup:** Requires more configuration and maintenance compared to managed solutions.<br>**Steeper Learning Curve:** Might need more expertise to manage effectively. |
| 4 | Harness | **Advanced CD:** Focuses on sophisticated deployment strategies, ideal for complex hybrid environments.<br>**Visibility:** Provides good insights into deployments and performance.<br>**Elastic Integration:** Can be integrated with Elastic Cloud, but might require custom configuration. | **Newer Platform:** Steeper learning curve compared to established options.<br>**Pricing:** Enterprise features can be costly. |
| 5 | CircleCI | **Speed & Ease of Use:** Cloud-based platform known for fast builds and intuitive interface.<br>**Hybrid Support:** Self-hosted runners enable on-premise deployments.<br>**Elastic Integration:** Possible through custom scripts or third-party integrations. | **CI/CD Focused:** Primarily for build and deployment.<br>**Cost:** Can be more expensive at scale compared to self-hosted options.<br>**Elastic Integration:** Might require more effort compared to other platforms. |

# Architecture Decisions

There are the major architecture decisions documented as Architectural Decision Records (ADRs):

- [ADR Mapping of domain to public cloud vs on-premise](./adrs/adr01-DomainsMapping.md)
- [ADR Select public cloud provider](./adrs/adr02-PublicCloudProvider.md)
- [ADR Select Migration Approach](./adrs/adr03-MigrationApproach.md)

# Risks and Technical Debts

This document presents a preliminary system design to address the specified challenge. The design prioritizes certain considerations over others, informed by my expertise and understanding. It is rooted in my current knowledge of relevant technologies and potential risks. However, it's important to recognize that this design serves as a high-level conceptual framework and might contain inaccuracies or omissions.

Following list highlights some illustrative examples of areas where the design could benefit from further refinement. It is not an exhaustive inventory of all potential gaps.

- **Analytics not covered:** The design does not yet incorporate a comprehensive analytics solution.
- **Network Setup not covered:** The network setup is not fully defined.
- **Comprehensive Data volume analysis:** The data volume analysis is not exhaustive.

In a real scenario, I would spend more time understanding the current situation and the motivation behind the task. 

# Glossary

# References
* Arc42 Template Created, maintained and © by Dr. Peter Hruschka, Dr. Gernot Starke and
contributors. See <https://arc42.org>.
