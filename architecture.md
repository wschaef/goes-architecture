# Introduction and Goals

## Requirements Overview

### Functional Requirements:

- **Cloud Migration:**
    - Migrate at least 90% of the company's existing on-premises applications and infrastructure to a hybrid cloud environment within 24 months.
    - Migrate the core e-commerce platform, including the website, order processing system, and payment gateway, to the cloud within the first 12 months.
    - Migrate the existing content library, including on-demand videos and audio content, to cloud-based storage within 9 months.
- **Hybrid Environment Support:**
    - Ensure seamless interaction and interoperability between on-premises and cloud-based components, with a latency not exceeding 100ms for 99% of transactions.
    - Establish secure and reliable communication channels (e.g., VPN, dedicated connections) between on-premises and cloud environments.
- **Data Management:**
    - Handle a daily data ingestion rate of at least 10TB, including user data, system logs, and content metadata.
    - Ensure data recovery time objective (RTO) of less than 4 hours in case of failure.
    - Maintain a data recovery point objective (RPO) of less than 24 hours.
    - Support efficient storage and delivery of large media files (videos, audio).
    - Handle various streaming protocols, including HLS and DASH, for adaptive streaming across different devices and network conditions.
- **Scalability:**
    - Handle a peak load of 100,000 concurrent users with an average response time of less than 2 seconds.
    - Automatically scale resources up or down based on real-time traffic patterns.
    - Handle spikes in bandwidth consumption during popular content releases or live events, ensuring uninterrupted streaming for all users.
- **Security:**
    - Encrypt all sensitive customer data, including personal information and payment details, both in transit and at rest.
    - Conduct regular security audits and penetration testing to identify and address vulnerabilities.
    - Ensure compliance with relevant security standards, such as PCI DSS for payment processing and ISO 27001 for information security.
    - Implement measures to prevent unauthorized access and distribution of copyrighted content using Digital Rights Management (DRM) technologies. 

### Non-Functional Requirements:

- **Performance:**
    - Maintain system availability of at least 99.99% (less than 53 minutes of downtime per year).
    - Ensure an average response time for user requests under 2 seconds under normal load conditions.
    - Achieve low latency streaming with a target latency under 5 seconds for live streams and under 3 seconds for on-demand content.
    - Maintain high video and audio quality across different devices and network conditions, adapting to varying bandwidth availability.
- **Scalability:**
    - Scale horizontally to accommodate a 100% increase in the user base and data volume within 1 month.
    - Dynamically scale bandwidth capacity to meet fluctuating demand, especially during peak hours and special events.
- **Cost-Effectiveness:**
    - Reduce the total cost of ownership (TCO) of the hybrid cloud environment by at least 15% compared to the current on-premises infrastructure within 3 years.
    - Implement cost optimization strategies for media storage and delivery, such as using different storage tiers based on content popularity and leveraging CDN caching.
- **Security:**
    - Ensure no unauthorized access to sensitive data is detected within a 12-month period.
    - Implement robust authentication and authorization mechanisms to protect user accounts and content.
- **Maintainability:**
    - Deploy new features or updates to the system within 2 weeks.
    - Ensure a mean time to repair (MTTR) of less than 2 hours for critical system failures.
- **Flexibility:**
    - Support the integration of new technologies and services within 3 months.
    - Adapt to new streaming technologies and formats as they emerge, ensuring compatibility with evolving industry standards. 

### Assumptions:

- **Scope:** E-commerce is fucused on streaming products
- **User Base:** The company has approximately 50 million active users, with a peak concurrency of 1 million concurrent users. 
- **Peak Load:** Peak load during holiday seasons or special events can result in a 5x increase in traffic compared to normal conditions.
- **Content Library:** The company has about 10,000 hours of content in its content library of on-demand video and audio content, distributed as follows: 5% SD, 70% HD, and 25% 4K. 

### Additional conciderations

- **Content Management System (CMS):** A robust CMS will be needed to manage and organize the content library, metadata, user subscriptions, etc. This system should integrate well with the cloud environment and support efficient content ingestion, processing, and delivery.
- **Recommendation Engine:** Implementing a recommendation engine could personalize content suggestions for users, potentially improving engagement and satisfaction. This might involve machine learning models and require large datasets for training.
- **Analytics and Monitoring:** Comprehensive analytics and monitoring are crucial for tracking user behavior, content performance, and overall system health. This might involve integrating with cloud-based monitoring tools and potentially developing custom dashboards for real-time insights. 

### Data volume analysis

#### Data domains

- **Customer Data:** This includes personally identifiable information (PII) such as names, addresses, contact details, and payment information. The sensitivity of this data necessitates robust security and compliance measures, potentially influencing the decision to keep some data on-premises in a hybrid model.
- **Transactional Data:** This encompasses data related to purchases, orders, payments, and shipping. The volume and velocity of this data, especially during peak periods like holidays, require a scalable and performant architecture.
- **Product Data:** This includes product descriptions, images, videos, reviews, and inventory information. The need to efficiently store, manage, and deliver this content to a global audience might drive the use of cloud storage and content delivery networks.
- **Streaming Data:** The streaming content aspect of the business generates real-time data related to user viewing habits, content performance, and network traffic. The architecture needs to handle the ingestion, processing, and analysis of this streaming data to gain insights and optimize content delivery.
- **Log and Monitoring Data:** The operation of both on-premises and cloud infrastructure generates logs and metrics related to system performance, security events, and application behavior. The architecture needs to collect, store, and analyze this data for monitoring, troubleshooting, and security purposes. 

#### Volume estimation

| Data Domain | Data Volume Store | Data Volume Write per Day | Data Volume Read per Day | Comment |
|---|---|---|---|---|
| Customer Data | 100GB | 1GB | 10GB | Assuming 1KB per user for PII and payment details, 1% daily churn rate, and 10% daily active users. |
| Transactional Data | 5GB | 5GB | 1GB | Assuming 10% of users make a transaction per day, 1KB per transaction, and 20% of transactions result in customer service requests. |
| Product Data | 11GB | 110MB | 5.5GB | Assuming 1KB text description, 1MB images, 10MB videos per product, 1% daily product updates, and 0.5 product views per user per day. |
| Streaming Data (Content Library) | 266.4 TB | 444 GB | 18 Tbps/hour | Assuming average bitrate of 5 Mbps across all resolutions and peak concurrency for 1 hour per day. 5% monthly content addition. |
| Log and Monitoring Data | 18 TB | 600GB | 5GB | Assuming 10KB logs per user per day, 100 GB server logs per day, and 1% of logs are accessed for analysis. 30-day retention. |


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
- Payment Provider - A third-party service that handles financial transactions for the system (e.g., processing payments from consumers).
- external IdP (3rd party IdP) - A third-party service that manages user identities and authentication for the system (e.g., allowing users to log in with their Google, Facebook, or other existing accounts).

# Solution Strategy

# Building Block View

## Whitebox Overall System

![Whitebox View of Overall System](img/Container.drawio.svg)

Motivation  
*\<text explanation>*

Contained Building Blocks  
*\<Description of contained building block (black boxes)>*

Important Interfaces  
*\<Description of important interfaces>*

### \<Name black box 1>

*\<Purpose/Responsibility>*

*\<Interface(s)>*

*\<(Optional) Quality/Performance Characteristics>*

*\<(Optional) Directory/File Location>*

*\<(Optional) Fulfilled Requirements>*

*\<(optional) Open Issues/Problems/Risks>*

### \<Name black box 2>

*\<black box template>*

### \<Name black box n>

*\<black box template>*

### \<Name interface 1>

…

### \<Name interface m>

## Level 2

### White Box *\<building block 1>*

*\<white box template>*

### White Box *\<building block 2>*

*\<white box template>*

…

### White Box *\<building block m>*

*\<white box template>*

## Level 3

### White Box \<\_building block x.1\_\>

*\<white box template>*

### White Box \<\_building block x.2\_\>

*\<white box template>*

### White Box \<\_building block y.1\_\>

*\<white box template>*

# Runtime View

## \<Runtime Scenario 1>

-   *\<insert runtime diagram or textual description of the scenario>*

-   *\<insert description of the notable aspects of the interactions
    between the building block instances depicted in this diagram.>*

## \<Runtime Scenario 2>

## …

## \<Runtime Scenario n>

# Deployment View

## Infrastructure Level 1

***\<Overview Diagram>***

Motivation  
*\<explanation in text form>*

Quality and/or Performance Features  
*\<explanation in text form>*

Mapping of Building Blocks to Infrastructure  
*\<description of the mapping>*

## Infrastructure Level 2

### *\<Infrastructure Element 1>*

*\<diagram + explanation>*

### *\<Infrastructure Element 2>*

*\<diagram + explanation>*

…

### *\<Infrastructure Element n>*

*\<diagram + explanation>*

# Cross-cutting Concepts

## *\<Concept 1>*

*\<explanation>*

## *\<Concept 2>*

*\<explanation>*

…

## *\<Concept n>*

*\<explanation>*

# Architecture Decisions

# Quality Requirements

## Quality Tree

## Quality Scenarios

# Risks and Technical Debts

# Glossary

| Term        | Definition        |
|-------------|-------------------|
| *\<Term-1>* | *\<definition-1>* |
| *\<Term-2>* | *\<definition-2>* |

# References
* Arc42 Template Created, maintained and © by Dr. Peter Hruschka, Dr. Gernot Starke and
contributors. See <https://arc42.org>.
