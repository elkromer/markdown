---
tags:
	- BIZTALK
	- Adapters
	- Pipeline
	- Components
---
# BizTalk Server
> BizTalk Server is a publish and subscribe architecture that uses adapters to receive and send messages, implement business processes through orchestrations, and includes management and tracking of the different parts. 

The BizTalk Server Engine has two main parts:

### Messaging Component
Provides the ability to communicate with a range of other software by relying on adapters for different kinds of communication. Support for creating and running graphically-defined processes called orchestrations. 

### Miscellaneous Component
A business rule engine that evalues a complex set of rules, a group hub that lets developers and administrators monitor and manage the engine and orchestrations it runs, and an SSO facility that provides the ability to map authentication information between Windows and non-Windows systems.

## The BizTalk Server Messaging Engine

Enables users to create business processes that span multiple applications. 

![Message Engine](https://docs.microsoft.com/en-us/biztalk/core/media/understandingbts-04-engine4.gif)

* A message is received into BSME through a **receive adapter**.
* A message is then processed through a **receive pipeline**.
* The message is delivered into a database called **MessageBox** which uses SQL Server.
* When a message arrives in the **MessageBox** that message is dispatched to its target **orchestration**. 
	* The result of this processing is likely another message produced by the orchestration and saved in the MessageBox.
* The message is then processed by a **send pipeline**
* The message is sent out using a **send adapter**

Another helpful visualization:

![Processing Workflow](https://docs.microsoft.com/en-us/biztalk/core/media/ebiz-dev-busprcsadptc.gif)

## Developing Custom Pipeline Components

Pipeline Designer is a graphical editor, hosted in Visual Studio which enables you to create new pipelines; view the pipeline templates included with BizTalk Server; move pipeline components within a pipeline; and configure pipelines, stages, and pipeline components.

1. Create a new `Empty BizTalk Server Project` in Visual Studio
2. Name it what you want the assembly to be named in the Pipeline Dropdown Menu in the Adapters (The first element in the square brackets).
	![Custom Pipeline Dropdown](resources/custompipelinedropdown.png)
3. Add a `New Item...` and name the pipeline component.
4. Configure the pipline component.
5. `Build` and `Deploy` the component to BizTalk Server. The component is added to the default application under "Pipelines". You might need to refresh.

Microsoft and other third-party companies that make BizTalk adapters also install pre-built pipeline components to BizTalk Server. Those are the ones that already appear in the Pipeline Dropdown Menu present in adapter configuration pages. They have just been built and deployed ahead of time so the developer does not have to.