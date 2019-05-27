# Case Management Overview

Red Hat Process Automation Manager enables you to automate different parts of your requirements. In previous labs we've seen how to automate the decision making to solve a Credit Card Dispute. In this scenario we will automate the full dispute process using Case Management.


## BPM and Case Management

### BPM

BPM is a management practice that views the _process_ as the most important organizing theme. It can be used only when processes are explicit, well defined, and repeatable. A practitioner of BPM talks about "optimizing a process". BPM allows organizations to explicitly model their process in an executable modeling language (Business Process Model and Notation, or BPMN2), allowing them to run their processes in and automated, repeatable and reliable way. Through the collection of metrics (key performance indicators, or KPIs) at runtime, the performance of the organizations processes can be analysed and possible improved.

In order to optimize a process, there must be a concrete representation of a given process; it must be applicable to many individual instances of the process; and the performance of the process must be measurable in terms of its KPIs. BPM is a practice of perfecting that process for the purpose of improving process performance of future instances.

This only works if you have confidence that future process instances will be like the instances of the past that you are measuring the process against.

In short, the process must be predictable. BPM is based on mass production principles: the up front investment that you make in perfecting the process, is paid back in a increase in efficiency over many instances of the process.

### Case Management

Case Management is a technique that is useful when processes cannot be fully defined up front, where the execution of the case instance is dynamic and data-driven, and/or when there is no structured path from the start of case instance to the completion of the case. A case represents a situation without necessarily requiring a structured process. A case can consist of smaller, repeatable, process fragments, that can either be structured, dynamic, or a mix of both.

Case management can be used for dynamic and unstructured processes, in which the execution flow of the process can not be (fully) determined in advance, and/or when this flow is dynamic and data-driven. Apart from the ability to explicitly define and express structured processes, case management requires a platform and toolset that allows practitioners to define data-driven actions and events, structured and unstructured process fragments, and the ability to define new fragments dynamically at runtime.

A Case Management Modeling and Notation (CMMN) standard has been defined by the OMG (Object Management Group, the same organization that is behind the BPMN2 standard). This language is aimed at modeling unstructured and dynamic cases, as opposed to BPMN2, which focusses on structured processes. Although Red Hat Process Automation Manager implements the CMMN specification, at Red Hat we believe that there is no well defined boundary between structured and unstructured processes. Instead, the space can be defined as a spectrum.

![Business Central RHPAM 7 CMMN PAM]({% image_path business-central-rhpam-7-cmmn-pam.png %}){:width="600px"}

As such, we believe that BPMN2, with a number of very subtle additions, is well suited to define processes and cases within the entire spectrum. In fact, there is an ongoing debate within the industry to [unify BPMN and CMMN](https://methodandstyle.com/bpmn-cmmn-compared/).

Some key differences between (traditional) Business Process Management and Case Management are:


## Case Roles

Case roles define the participants that will be involved in case handling. Case roles are defined on a case definition level to make the case definition independent from the actual actors involved in a given case instance. These roles can be assigned at case instance start time, and can by be dynamically changed at runtime. These rules are independent of the a user's existing roles in, for example, the corporate Identity Management System (IDM).  This means that a case participant can have a different case roles in different case instances. E.g. in one case instance, a participant can have the role of _case handler_, while in another case instance he/she can have the role of _case supervisor_.

Some characteristics of Case Roles are:

- Different from BPM roles in the sense that they can be dynamically assigned at case instance start or changed during runtime.

- Specific authorization on case instances can be assigned to case roles (e.g. the authorization to add a task to a case, to add data to a case, to cancel a case, etc.).

- Used for Human Tasks to interact with the engine.

- Case roles are defined on case definition level to make the case definition independent from the actors involved.

- Case roles are typically assigned when a new case instance is created and started.

- Can be modified at any time as long as case instance is active though it will not have effect on tasks already created based on previous role assignment.


## Milestones

Milestones are part of the case definition and keep track of important achievement within a case instance. Some characteristics are:

- Milestone can use Case File information as a condition to trigger

- Only when a milestone is completed, it will follow to the next node. Milestones don't implement any functionality.

- Initial milestones are usually marked to be automatically enabled via the Adhoc-Autostart property. An enabled milestone will check its conditions when events change the case instance and case data.

- Subsequent milestones can be activated at any time during the execution of the case instance, simply by signalling the milestone. Note that signalling a milestone will not complete it. It will simply activate its conditional completion expression.


## Case Stages

A stage encompasses a set of activities that are logically bound together.

- Several stages can run in parallel, and you can trigger the same stage several times.

- A conditional expression defines the completion logic of a stage, using the information in the Case File.

- Stages can be conditionally triggered (started) via an expression. This allows for dynamically, data-driven, activation of stages.


## Dynamic Activities

In the context of Case Management means that the process definition that is behind a Case has no such dynamic activities. You can add this type of activities when the case is started and running, they are discretionary activities. Some characteristics of Dynamic Activities are:

- Cases are not structured, they are dynamic and provide room for dynamic tasks
- Since dynamic tasks do not have data output definition there is only one way to put output from task/subprocess to the process instance - by name. This means the name of the returned output of a task must match by name process variable to be mapped.
- Dynamic activities are

   - user task
   - service task
   - sub process - reusable
