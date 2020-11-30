# 3. Case Management Overview

Case Management is a useful technique used when processes cannot be fully predicted and defined up front. Some of the common scenarios of Case Management is to have a dynamic and data-driven execution, where there is no structured path from the start of case instance to the completion of the case. A `case` represents a situation without necessarily requiring a structured process. A `case` can consist of smaller, repeatable process fragments, that can either be structured, dynamic, or a mix of both.

A Case Management Modeling and Notation (CMMN) standard has been defined by the OMG (Object Management Group, the same organization that is behind the BPMN2 standard). This standard is aimed at modeling unstructured and dynamic cases, as opposed to BPMN2, which focusses on structured processes. Although Red Hat Process Automation Manager implements the CMMN specification, at Red Hat we believe that there is no well defined boundary between structured and unstructured processes. Instead, the space can be defined as a spectrum.

![Business Central RHPAM 7 CMMN PAM]({% image_path business-central-rhpam-7-cmmn-pam.png %}){:width="800px"}

As such, we believe that BPMN2, with a number of very subtle additions, is well suited to define processes and cases within the entire spectrum. In fact, there is an ongoing debate within the industry to [unify BPMN and CMMN](https://methodandstyle.com/bpmn-cmmn-compared/).

## Case Management Terminology

Case Management introduces a number of additional concepts and terminologies on top of business process management.

In this lab there are a number of key components that we will use, which we will introduce here:

- _Case File_: Specific to case management. The Case information is represented by the _Case File_. It contains _Case File Items_ that can be any type of data structure, _Case File_ serves as the context for raising events and evaluating Expressions, as well as point of reference to guide the execution of the process. All the data, including documents that we need to get to be able to solve the dispute, will be stored in the _Case File_.
- _Stage_: Specific to case management.  Stages may be considered “episodes” of a Case, though Case models allow for defining Stages that can be planned in parallel as well. You would normally group tasks that logically belong together in a stage.
- _Milestone_: Specific to case management. Milestone represents an achievable target, defined to enable evaluation of progress of the Case. No work is directly associated with a Milestone, but completion of set of tasks or the availability of key deliverables (information in the Case File) typically leads to achieving a Milestone.
- _Human Task_: Is a Task that is performed by a Case worker.
- _Process Task_: Can be used in the Case to call a Business Process.
- _Decision Task_: Can be used in the Case to invoke a Decision. A Decision in CMMN is an abstraction of Decisions as they are specified in various Decision Modeling specifications.

As mentioned before, a case has a more dynamic lifecycle than a regular process. In a regular process the final state is: finalized. This can be due to completeness, error, or abortion of the process instance, but once the process is terminated you can't restart it. This is different in a case management platform. In case management, case owners are able to, for example, re-open a completed case. A use-case can, for example,  be to re-open a dispute case when the credit-card holder is not happy with the outcome of the dispute. In the following picture we can see the lifecycle of a case and the operations we can execute agains it.

![Business Central Case  Lifecycle]({% image_path business-central-case-lifecycle.png %}){:width="400px"}

## Conclusion

The capability of adding tasks during runtime, having a case file shared among all instances, having dynamic roles per case instance, and being able to track the achieved milestones of an ongoing work, are common requirements use cases among different sector like finance and health care. 

Now that you are aware of some of the concepts involved on a case management definition, let's start with some authoring.
