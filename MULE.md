---
tags:
  - MULE
  - MULESOFT
---

# MuleSoft Connectors

A suite of Java connectors and Modules that extend the features of MuleSoft with Internet Communications and secure messaging capabilities. MuleSoft is just the name of the company... to understand how they are used you need to know the different MuleSoft products.

### Background

**What are all the MuleSoft products?**

MuleSoft, LLC. originally provided middleware and messaging and later expanded to provide an integration platform as a service approach for companies through its main product, Anypoint Platform. Anypoint Platform contains various components such as the Anypoint Design Center, Anypoint Exchange, and Anypoint Management Center.

> Anypoint Platform helps you build a structured application network that connects applications, data, and devices with reusable APIs. Instead of retrieving random and possibly unstable code snippets, you can “shop” for APIs created using the industry’s best practices.

- `Anypoint Platform`: MuleSoft’s product to design, build, and manage APIs and integrations.
- `Anypoint Exchange`: MuleSoft repository for connectors, templates, examples, and RAML APIs. Discover and use proven assets built by the MuleSoft ecosystem. You can create your own Exchange and add assets to it for collaboration and sharing among a group.
- `Anypoint Design Center`: A cloud-based development environment for Mule runtime engine (Mule) applications and API definitions.
- `Anypoint Management Center`: A single web interface to administer Anypoint Platform, both on-premises and in the cloud. Manage APIs and users, analyze traffic, monitor SLAs, and troubleshoot underlying integration flows.

**What makes Mule Mule?**

MuleSoft also offers the Mule runtime engine, a lightweight enterprise service bus and integration framework for building in-house applications. It can broker interactions between other platforms such as .NET using web services or sockets. Supporting software include the Anypoint Studio IDE and [Runtime Manager](https://docs.mulesoft.com/runtime-manager/) (facilitates deployment to cloudhub or on-premise server).

- `Mule runtime engine`: Java-based integration runtime engine for AnyPoint Platform.
- `AnyPoint Runtime Manager`: A console where you deploy and manage applications built with any Mule. Deploy to servers in the cloud (currently handled by CloudHub) or on premises. Access the console in Anypoint Platform or download it as a standalone program that runs in a local server. Previously referred to as ARM.


Enterprise design patterns supported by MuleSoft: 
https://www.enterpriseintegrationpatterns.com/patterns/messaging/toc.html

## Mule Applications

`Mule` is an engine that runs Mule applications. Applications connect systems, services, APIs, and devices using an API-led connectivity instead of P2P integration. Anypoint Studio and the Flow Designer support Mule application development.

For a Mule applicaiton to run, it needs to be deployed where the Mule runtime engine is installed. It comes packaged into the Studio IDE and in Design Center on Anypoint Platform so you can run a Mule app as you design it.

### Mule Components and Connectors

There are two main types of components:
- Core Components 
	+ Part of the core functionality of Mule runtime.
- Connectors
	+ Group together components (a.k.a modules) that were created to facilitate integration with external resources.

Core components and connector components are the main building blocks of applications. Components execute business logic on the messages that flow through your Mule application. Components are on the right side of the palette, connectors are on the left side.

![Components](/resources/mule-components.png)

**Connector Architecture**

There are two basic connector structures in Mule: connectors that are endpoint-based and connectors that are operation based. 

Endpoint-based connectors pass messages into and out of a Mule flow, usually to external resources or other Mule flows. 

Operation-based connectors transfrom a message or do some processing on a message passing through a flow.

### Flows

An app can consist of a single flow or it can break processing up into discrete flows and subflows that you connect together. You can connect and trigger their execution with Flow Reference components or by using the DataWeave `lookup` function within expressions and transformations.

**Flow Reference Example:**

![Flow Reference](/resources/mule-flow-reference.png)

The contents of a subflow replace each Flow Reference component that references that subflow.

### Mule Configuration File

All mule applications, domains, and policies are configured through an XML file. When first starting AnyPoint this is the design-time language of the visualized flow. The file specifies the resources that compose the artifact, including dependencies. Although you can write the XML file manually, it is more common to use the graphical user interfaces in Anypoint Design Center or Anypoint Studio to structure and define the behavior of your Mule app.

**Structure of the design-time language**

```
A Mule configuration file is a tree.
Each of the elements sets up a configuration object within Mule, for example:

    Mule Global Configuration
    Global settings, such as the default transaction time-out, that apply to the entire Mule configuration.

    Properties
    Configuration Properties, message properties, and system properties.

    Flows
    Combine components to define a message flow.

    Sources (Endpoints or Triggers)
    Trigger a flow. Sources are sometimes called Endpoints in Studio and Triggers in Flow Designer.

    Connectors and Modules Configurations
    Declare configurations for any connectors and modules components used.

    Routers
    Control the flow execution.

    Operations
    Apply specific actions within a flow.
```

### Mule Events

Events contain the core information processed by the runtime. It travels through components inside your app folling the configured application logic. The Mule Event contains these objects:

![Events](/resources/mule-events.png)

- A mule message containing the message payload and attributes
- Variables are Mule event metadata that you use in your flow

A mule event is generated when a trigger reaches the Event source of a flow. The trigger could be an event external to the mule app. It reaches the event source, which produces a Mule event. The event travels sequentially through the components of a flow. Each component interacts in a pre-defined manner with the Mule event.

![Event Flow](/resources/mule-event-flow.png)

### Mule Errors

All errors belong to one of the two main types `ANY` or `CRITICAL`. Each type under `ANY` is matched by its parent and can be handled, while error types under `CRITICAL` are so severe that they cannot be handled and are only logged.

![Errors](/resources/mule-errors.png)

In each operation of your flow you can map the possible error types to a custom error type of your choosing. You can use custom error types to differentiate where an error occurred in your flow. 

You can use an On-Error component to "catch" the error in your flow. When an error occurs in a mule app, an error handler component routes the error to the first On-Error component configuration that matches the mule error.

![On-Error](/resources/mule-on-error.png)

### DataWeave Language

DataWeave is the MuleSoft expression language for accessing and transforming data that travels through a Mule app. It is tightly integrated with the Mule runtime engine. 

DataWeave scripts act on data in the Mule event. Most commonly, you use it to access and transform data in the message payload. For example after a component in your app retrieves data from one system, you can use DataWeave to modify and output selected fields in that data to a new format then use another component to pass on that data to another system.

To run scripts, you copy them into the source code area of a Transform Message component in Studio, then view the results in the component's `Preview` pane

![DataWeave](/resources/mule-dw.png)

## Deploying Mule Applications

Three different deployment targets are supported: Cloudhub, Anypoint Runtime Fabric, and on-premises Mule instances. The Mule runtime engine instances are automatically taken care of in Cloudhub and Anypoint Runtime Fabric.

### Cloudhub

Cloudhub is an integration platform as a service where you can deploy and manage an application in the cloud.

Configure the deployment strategy in your project's `pom.xml` file so you can deploy, redeploy, and undeploy your mule application using Mule Maven plugin. 

```xml
<plugin>
  <groupId>org.mule.tools.maven</groupId>
  <artifactId>mule-maven-plugin</artifactId>
  <version>3.3.2</version>
  <extensions>true</extensions>
  <configuration>
    <cloudHubDeployment>
      <uri>https://anypoint.mulesoft.com</uri>
      <muleVersion>${app.runtime}</muleVersion>
      <username>${username}</username>
      <password>${password}</password>
      <applicationName>${cloudhub.application.name}</applicationName>
      <environment>${environment}</environment>
      <properties>
        <key>value</key>
      </properties>
    </cloudHubDeployment>
  </configuration>
</plugin>
```
**Deploy**

`mvn clean package deploy -DmuleDeploy`

**Redeploy**

`mvn clean package deploy -DmuleDeploy`

**Undeploy**

`mvn mule:undeploy`

**CloudHub Architecture**


![Cloudhub](/resources/mule-cloudhub.png)

**Logs**

CloudHub provides access to log data such as deployment messages and events. For general monitoring, searching across log files, and archiving  of logs use `Anypoint Monitoring`. 

If your app has its own logging system, you can disable cloudhub logs and integrate your CloudHub application with your own logging system using `Log4j` configuration.