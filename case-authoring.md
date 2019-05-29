
# First steps to author a case.

You will learn in this section:

1. What are Milestones and when to use them.

2. How to model a case using Milestones.

3. How to apply the CMMN specification to Case Modeling.

## The Credit Card Dispute case

![Business Central CC Dispute Processing Backoffice Processing]({% image_path business-central-cc-dispute-diagram-users.png %}){:width="600px"}


## Case Management Terminology

Case Management introduces a number of additional concepts and terminologies on top of business process management. In this workshop there are a number of key components that we will use, which we will introduce here:

    - _Case File_: The Case information is represented by the _Case File_. It contains _Case File Items_ that can be any type of data structure, _Case File_ serves as the context for raising events and evaluating Expressions, as well as point of reference to guide the execution of the process. All the data, including documents that we need to get to be bale to solve the dispute, will be stored in the _Case File_.

    - _Stage_:  Stages may be considered “episodes” of a Case, though Case models allow for defining Stages that can be planned in parallel as well. You would normally group tasks that logically belong together in a stage.

    - _Milestone_: A Milestone represents an achievable target, defined to enable evaluation of progress of the Case. No work is directly associated with a Milestone, but completion of set of tasks or the availability of key deliverables (information in the Case File) typically leads to achieving a Milestone.

    - _Human Task_: Is a Task that is performed by a Case worker.

    - _Process Task_: Can be used in the Case to call a Business Process.

    - _Decision Task_: Can be used in the Case to invoke a Decision. A Decision in CMMN is an abstraction of Decisions as they are specified in various Decision Modeling specifications.

Another characteristic of a case is that it has a more dynamic lifecycle than a regular process. In a regular process the final state is: finalized. This can be due to completeness, error, or abortion of the process instance, but once the process is terminated you can't restart it. This is different in a case management platform. In case management, case owners are able to, for example, re-open a completed case. A use-case can, for example,  be to re-open a dispute case when the credit-card holder is not happy with the outcome of the dispute. In the following picture we can see the lifecycle of a case.

![Business Central Case Lifecycle]({% image_path business-central-case-lifecycle.png %}){:width="600px"}

## Milestones of the Case

In order to track the progress of a case instance, we need to define the relevant milestones for the customer. A milestone has to be defined to inform the Credit Card Holder that an important goal has been achieved. Some of these goals can be attained in parallel. After you have completed a milestone, there is still a possibility you can go back in the process and trigger the milestone again if needed. Also some of the milestones are discretionary, so they might never be triggered. A Milestone in a case definition is represented by the  Milestone node .

We've identified the following milestones in our Credit Card Dispute case:

1. Dispute started

2. Documentation received from CC Holder

3. Automated Chargeback processing

4. Standard processing

5. Account credited

6. Dispute rejected


These are the achievable targets to help us track the progress of the dispute. They don't have any particular order, so you can come back to any of them if something in the Case File data changes.

Next you will learn how to model Milestones inside your Case Definition.


## Defining Milestones

Login to the Business Central workbench.

In the previous step we have defined the case variables, if you did not complete that step, you may import the following repository. You can watch the video, presented earlier, on how to import a repository into your workspace

  [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-2](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-2).

To model the milestones of the case:

1. Open the `ChargeDispute` process.

2. Select from the Object Library Panel a Node of type _Start Event_ then add a _Script Node_ connected to this start event. On the properties panel for the _Script Node_ enter the following:

    Name: `Log Case Started`
    Script  `System.out.println("Case started");`

    ![Business Central Designer Script Task]({% image_path business-central-designer-script-task.png %}){:width="600px"}

    Add an _End Event_ of type _Signal_ and set the signal name to _“Dispute Received”_, so once you've completed the logging that the case has started the signal will trigger a Milestone called Dispute Received. Note that triggering the _Milestone_ only activates it, it does not complete it. A _Milestone_ is completed when its _Condition_ is met.

    Signal Ref:  `Milestone 1: Dispute received`

    ![Business Central Designer Script Task End Event]({% image_path business-central-designer-script-task-end-event.png %}){:width="600px"}

3. Add a _Milestone_ node. Note that for the _Milestone_ to be triggered by the _Signal End Event_ we created earlier, the _Signal Ref_ of the event should have the exact same name as the _Name_ of the _Milestone_. We set the condition to trigger when the `fraudData` case file item is not `null`.

    **Note**: You can set the `Condition` of a _Milestone_ in the _Assignments_ properties of  the _Milestone_ node. Simply select the node, and click on the `Assignments` field of the property editor (the panel on the right side of the screen). This will open the _Data Input/Output Assignments_ editor. The data-input `Condition` should already be listed. In the _Source_ field, select `Constant`, and type (or paste) the condition expression.

    Name:  `Milestone 1: Dispute received`

    Condition: `org.kie.api.runtime.process.CaseData(data.get("fraudData") != null)`{{copy}}  

    Adhoc autostart: `false`

    ![Business Central Designer Milestone Dispute Received]({% image_path business-central-designer-milestone-dispute-received.png %}){:width="600px"}

    Ad hoc nodes with no incoming connections, like the _Milestone_ we've just defined, can be configured with the `Adhoc autostart` property, which is a property of the node itself. These tasks are triggered automatically when the case instance is started. Triggering a node means that the node becomes _active_, not that they are completed. In the case of a Milestone this means that the milestone becomes active and that its conditional completion expression will be enabled. However, in this case, we are setting `Adhoc autostart` to `false`, and thus configuring the Milestone to be triggered by an event and not automatically.

4. Save your process/case definition.

5. Go back to your project's _Asset Library_ view. Click on the _Deploy_ button in the upper right corner of the screen. This will package and deploy your project to the Execution Server. The workbench will display 2 green notification bars, stating the build and deployment were successful.

6. Go to _Menu -> Manage -> Process Defitions_. You will see you `ChargeDispute` listed.

    ![Business Central Process Definitions Charge Dispute]({% image_path business-central-process-definitions-chargedispute.png %}){:width="600px"}

-------

Note: As part of the pre-defined project, we've already created the Case Start Form, which will allow you to start the case and provide its input data.

-------

7. Click on the kebab icon on the right side of the process/case definition. Click on _Start_ to start the case. This will open the case start form that was provided by us, and which allows you to enter the data to your first case instance.

    ![Start Charge Dispute Case]({% image_path start-chargedispute-case.png %}){:width="600px"}

8. The start form allows us to specify the mappings for the case roles and groups, as well as the Credit Card Dispute input data for our case. Fill in the following details (make sure to set the `owner` to `pamAdmin`. Failing to do so will prevent the case from being displayed in our case app for your `pamAdmin` user). When you've filled in the input data, click on the _Submit_ button, which will start your case.

    ![Start Charge Dispute Case Form 1]({% image_path start-chargedispute-case-form-1.png %}){:width="600px"}

    ![Start Charge Dispute Case Form 2]({% image_path start-chargedispute-case-form-2.png %}){:width="600px"}

    ![Start Charge Dispute Case Form 3]({% image_path start-chargedispute-case-form-3.png %}){:width="600px"}


9. The screen will automatically change to the _Process Instance Details_ screen of the process/case you've just started. Click on the _Diagram_ tab to see the diagram of your case, and the nodes that have been traversed in your case instance. Note that the _Milestone_ was both triggered (by the _Signal End Event_), as well as completed (by the _Condition_ that evaluated to `true`).

    ![Start Charge Dispute Diagram]({% image_path start-chargedispute-diagram.png %}){:width="600px"}

10. Click on the _Process Variables_ to get an overview of the current state /values of the Case. Note that `fraudData` is indeed set, which was the _Condition_ that completed our _Milestone_.

    ![Start Charge Dispute Case Variables]({% image_path start-chargedispute-case-variables.png %}){:width="600px"}

11. The platform also provides a _Case Management Showcase_ application, which is an example of how a custom Case Management dashboard could be composed. Open this application by clicking on the _Application Launcher_ button on the top right of the screen. Next, click on _Case Management Showcase_. Login with the same credentials that you used for the Business Central Workbench (u:pamAdmin, p:redhatpam1!).

    ![Business Central Case Management Showcase Button]({% image_path business-central-cm-showcase-button.png %}){:width="600px"}

12. The showcase application will list the current open cases. You will see the case you've just started in the list:

    ![Case Management Showcase ChargeDispute List]({% image_path cms-chargedispute-case-list.png %}){:width="600px"}

13. Click on the case to open a detailed view of the open case. This view will, among other things, list the completed milestones, the available actions, the actions that are in progress, the completed actions and the case roles. Take some time to explore the details of your case.

    ![Case Management Showcase ChargeDispute Details]({% image_path cms-chargedispute-case-details.png %}){:width="600px"}




In this example we saw that Milestone can be triggered by signals, you can add logic after a milestone that will execute when the node is triggered. Another way to trigger a Milestone is when a condition on the data of the CaseFile is met.

4- Add a second Milestone

Name:  `Milestone 2: Customer doc received`{{copy}}  
Ad hoc autostart: false

5- Look in the properties panel for the assignments section and click on the V icon, select the source property on the input assignment condition.
Select constant as the value and type

`org.kie.api.runtime.process.CaseData(data.get("customerDocReviewed") == true)`{{copy}}

![Business Central Designer Milestone Docs Received]({% image_path business-central-designer-milestone-docs-received.png %}){:width="600px"}

In here we are checking that the variable customerDocReviewed is true to trigger Milestone 2: Customer doc received and consider that target as achieved. We will repeat the same process for the other milestones we defined at the beginning.

Name:  `Milestone 3: Automated Chargeback`{{copy}}  
Condition:`org.kie.api.runtime.process.CaseData(data.get("caseType") == "automated")`{{copy}}  
Ad hoc autostart: false

Name:  `Milestone 4: Standard Processing`{{copy}}  
Condition:`org.kie.api.runtime.process.CaseData(data.get("caseType") == "standard")`{{copy}}  
Ad hoc autostart: false

These Milestones will be triggered by a signal

Name:  `Milestone 5: Account credited`{{copy}}  
Condition:none  
Ad hoc autostart: false  

Name:  `Milestone 6:  Dispute rejected`{{copy}}  
Condition:none  
Ad hoc autostart: false  

Here you have learned how to use Milestones in your case, now you are able to track the progress of the case using these milestones. To import the solution repository delete your current project and import:

https://github.com/MyriamFentanes/case-management-scenario-step4.git
