# What is Activiti Cloud?
Activiti Cloud is the first Cloud Native BPM framework built to provide a scalable and transparent solution for BPM implementations in cloud environments.

The BPM discipline was created to provide a better understanding of how organisations do their work and how this work can be improved in a iterative fashion. BPM Suites were created on the basis that a company will host a central BPM Server which will be in charge of automating and monitoring their business processes. This approach caused several frustrations in modern organisations, the following list highlights some of the pain points that adopting a BPM Suite represent for medium sized org:
- BPM Suites are built on top of a technology stack that the IT Department (Software) inside the organization doesn’t need to know
- BPM Suites doesn’t integrate nicely with other ecosystems
- Users needs to learn a whole new tool to do the work
- The department in charge of the infrastructure where the BPM Suite will run doesn’t know about its requirements. Same applies for DBAs that need to understand how the BPM Suite works in order to tune their Databases.
- User Interfaces provided by BPM Suites are not flexible enough. Most of the time BPM implementations end ups causing multiple UIs reimplementations
- BPM Suites adoptions are usually pushed from the business and not backed up by the internal software development teams inside the organization 

Most of these pain points are due because BPM Suites impose a whole set of technologies, pushing organisations to adapt to them. This, of course, cause rejections, pain and frustration. 

With the rise of cloud environments and tools that facilitates the adoption of such environments (such as microservices, containers and service orchestrators) BPM Suites are nowadays forced to redesign themselves to fit nicely in these environments. 

Containers helps to ease the pain on the technology side. We are now responsible for containers that hides whatever software that is running inside. But containers are not enough. The main problem of BPM Suites is that most of them were designed as monoliths, pushing the adopters to go for all or nothing. On the other side of the spectrum, you will find Open Source BPM Frameworks which aim to be as generic as possible to support a wide range of development and deployment scenarios. By doing this, these projects are troublesome to use, to maintain and to fit into different architectures. Because of this generic approach, Open Source BPM frameworks delegate too many decisions to the one implementing the solutions, pushing them to not only learn about the internals of the framework but also to make complicated decisions that only experts can make accurately. 

Activiti Cloud is an attempt to strip down the Activiti Process Engine to its bare minimum and to keep it as single focused as possible. At the same time Activiti Cloud provide a set of well defined and focused services required for most BPM implementations. Each of these services are ready to be used, but they are all independent from each other. You can choose what you need and what you don’t or even replace implementations if the one provided doesn’t fit your needs.

The main objective of the Process Engine from the Activiti Cloud perspective is to understand (parse) BPMN 2.x business process definitions and automate their executions (runtime / process execution). 

The Process Engine shouldn’t worry about:
- Where the process definitions are stored
	- Dealing with process versions and changes
- Identity Management 
- Single Sign On
- Job executions
- Timer mechanisms
- Providing System to System integration mechanisms
- Sending Emails
- Storing History / Audit information and providing a way to query this information
- Performance of the clients consuming data generated by the engine

Based on these list of things that the Process Engine shouldn’t do, we have created  different Activiti Cloud components and adopted 3rd party components that will interact with the process engine to provide all these functionalities that are required 90% of the time when we want to implement a BPM project. 

Activiti Cloud provides an opinionated way of deploying these components to enable your implementation to scale using production ready components from the moment you start your implementation. In addition to provide a recommended way of deployments we are making sure that using our tools feel natural for different personas (developers / devops / end users):
- Spring Boot / Spring Cloud: if you are application and developers are already using these technologies, adding Activiti Cloud to the mix should be straight forward
- If you are looking into technologies such as Kubernetes and Docker, all our components are ready to use and aligned with the requirements of these environments. 
- If you want to change/customize some of the components that are provided out of the box you can by using our *-cloud-starters.
- If you want to change the underlaying technology stack, for example switch RabbitMQ to ActiveMQ or Kafka you can because we rely on Spring Cloud abstraction layers.
- If you worry about process/application migration and updates, you can rely on industry standard ways of dealing with container versions and data migration, instead of dealing with complicated migration tools writing specifically for the Process Engine.
- If you already have a Continuous Integration / Deployment pipeline you can integrate BPM specific builds with those tools. 


# Spring Cloud + Activiti Cloud
From the Activiti framework point of view we rely in 3 key aspects from Spring Boot / Spring Cloud which enable our components (such as the process engine) to integrate nicely with the rest of the infrastructure. 

First of all we rely on Spring Boot HATEOAS for all of our REST endpoints making sure that they are aligned and we have minimal code to maintain. 

Secondly, we rely heavily on Spring Cloud Stream to make sure that we can consume and emit events in an asynchronous manner. This is a key enabler for making sure that independent components can collaborate without being tightly coupled. 

Finally, we leverage the abstraction layers on top of several services such as Service Registry, Gateway for dynamic service registration, discovery and routing, Distributed Configuration Service, etc.

We leverage all the components to make sure that we run nicely in distributed environments with technologies such as Docker and Kubernetes. We are also making sure that Activiti Cloud doesn’t overlap with any of the features provided by such technologies to not cause any mismatch. 

# Activiti Cloud Components
Activiti Cloud provides a set of basic building blocks part of them are infrastructural services that will enable the whole platform to work and part of them are BPM specific. All these components were designed to be scalable independently one from the other and you are free to re-arrange them or replace them with your existing infrastructural services.

The following components are currently being developed:
- Infrastructure
	- Activiti Cloud Gateway
	- Activiti Cloud Registry
	- Activiti Cloud SSO / IDM (Based on BPM requirements)
	- Storage / Databases
	- Message Brokers
- Activiti Cloud Applications
	- Activiti Cloud Query Service (BPM Specific)
	- Activiti Cloud Audit Service (BPM Specific)
	- Activiti Cloud Notification Service
	- Activiti Cloud Runtime Bundles (Domain Specific)
	- Activiti Cloud Connectors (Domain Specific)

We define a set of services that should be provided by the infrastructure, meaning that in different environments these components can be replaced by services provided by the underlaying infrastructure. 
Other components will rely on these infrastructural services to work, meaning that a clear set of features must be provided for the Activiti Cloud Applications to work with all the designed features. 

![](/assets/Screen Shot 2017-09-11 at 09.07.43.png)

Activiti Cloud Applications are dynamic and they can be provisioned on runtime. We use runtime bundles to define application scopes. These Runtime Bundles encapsulates the Process Runtime and a fixed set of business processes (and versions) that we want to automate. 

Following this approach, we will have two different Runtime Bundles if we decide to change one or more versions of our business processes. 

Each of these Activiti Cloud Applications can define where to store the runtime state of its processes. This allows extreme flexibility on configuration to support a wide range of scenarios, but it also delegates the responsibility to the architects defining how the implementation will work. In order to simplify these decisions, sensitive defaults will be provided. 

![](/assets/Screen Shot 2017-09-09 at 10.15.24.png)

You can find examples and tutorials on how to use these services for your applications <HERE>.

You can also find <HERE> an example scenario using these technology.

# Activiti Cloud and Activiti 6.x and 5.x
Activiti Cloud uses as is core Activiti 7.x which is an evolution from Activiti 6.x. But as it was mention before, Activiti 7.x will bring some changes in how we recommend to do things. Most of these changes will be simplifications to make sure that the framework itself doesn’t promote practices that has been recognised to cause huge pains on real life implementations. All these changes and simplifications are done with cloud environments in mind. If you are using Activiti 6.x or 5.x and it is working fine for you, don’t worry Activiti 7.x will work for you. But if you are looking into microservices and cloud environments and you are suffering to adopt BPM tools and frameworks Activiti Cloud will solve most of those problems for you.
It is also important to note that Activiti Cloud is built on top of award wining frameworks that are widely used in the industry, for this reason, you don’t need to hire specialised developers to extend and improve Activiti Cloud services. 

Activiti Cloud is being built by engineers coming from different BPM and industry backgrounds and we are aiming to solve problems that are not being solved by these other proprietary or open source projects. 

The team behind Activiti Cloud is very committed to Open Source practices and we will adopt and integrate our services with other Open Source projects that we believe that our community will benefit from. We will also participate and collaborate with other Open Source projects that share our interests in an Open Collaboration fashion. If you are working in another Open Source project are you are interested to integrate with us or to work in collaboration, please get in touch. 




