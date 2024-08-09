### Part 1: Architectural Strategy for Cloud Transformation (30-45 min)

#### Scenario

* **Your Role:** Cloud Consultant
* **Company Business Profile:** Global, On-line E-Commerce and Streaming Content Provider (GOES)
* **Current State Technology Architecture:** On-premises (multiple physical data centers) that are self-managed

![Current State Architecture](img/current_state_architecture.png)


The company (GOES) has made a strategic decision to transition to a hybrid cloud environment. You will be presenting to the panel of interviewers who will role-play the following stakeholders:

1. Application Stack developer
2. Enterprise Architect
3. VP Engineering
4. A Business Person of the solution internal to our company

#### Objectives

Choosing a cloud provider of your preference, guide us through the architectural transformation this company needs to undertake to successfully transition to the cloud. Key objectives are:

* To design an architecture using cloud-native patterns that effectively supports the transition of existing on-premise applications and infrastructure to the cloud.
* To address the challenges of ensuring seamless functioning in a hybrid environment.
* To identify and discuss potential risks associated with moving to the cloud and propose strategies to mitigate them.
* To highlight the opportunities that the cloud provides over their existing self-managed on-premise setup, outlining the potential benefits and enhancements the company could enjoy.
* To demonstrate a comprehensive understanding of key considerations the company should have during this transition, such as cost, security, scalability, and data management.

This segment doesn't specifically require the inclusion of Elastic. The focus is on your overall architectural understanding, cloud migration strategy, and ability to identify and address potential risks and opportunities. We not only want to hear about the high-level architecture but also technical details that show you are able to go deep. Again, this doesn't have to be Elastic-centric of course.

#### Considerations and attributes

The e-commerce company operates globally, has a peak load during holiday seasons, and maintains sensitive customer data due to its nature of business.

* **Size and Scale:** The e-commerce company is a large multinational business, with operations across different continents. They have millions of active users on their platform, which results in a substantial amount of data being generated daily.
* **Data Privacy and Compliance:** The company operates in regions with strict data privacy regulations (such as the GDPR in Europe), which influences how and where they can store and process their data.
* **Complexity of Services:** The company offers a variety of services (e.g., online shopping, content streaming, online payment services) which increase the complexity of the architecture.
* **Peak Periods:** The e-commerce company experiences significant spikes in traffic during certain periods (like Black Friday or Christmas), which requires a robust and scalable architecture to handle these peak loads without any disruption to the services.
* **Business Continuity Requirements:** The nature of their business requires a high degree of availability and resiliency. Any significant downtime or service disruption could result in substantial revenue losses.
* **Innovation Mindset:** The company has a culture of innovation and frequently experiments with new features and services to improve customer experience. This means their architecture needs to support rapid changes and deployments.

By taking these attributes into account, you should demonstrate an understanding of how these factors shape the architectural decisions in a cloud environment.

Please prepare diagrams to visualize your architectural design during your presentation. These diagrams can represent a step-by-step transformation process, or various aspects of the final architecture. This will help us better understand your thought process and the key decisions that you have made in designing this transformation.
