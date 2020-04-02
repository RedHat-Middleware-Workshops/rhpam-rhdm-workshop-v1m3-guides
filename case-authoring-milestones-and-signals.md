
# Case Authoring - Milestones and Signals

You will learn in this section:

1. What are Milestones and when to use them.

2. How to model a case using Milestones.

3. How to activate Milestones using Signals.

4. How to complete Milestones.

## Milestones of the Case

In order to track the progress of a case instance, we need to define the relevant milestones for the customer. A milestone has to be defined to inform the Credit Card Holder, case owner and case workers that an important goal has been achieved. Some of these goals can be attained in parallel. After you have completed a milestone, there is still a possibility you can go back in the process and trigger the milestone again if needed. Also some of the milestones are discretionary, so they might never be triggered. A Milestone in a case definition is represented by the  Milestone node .

We've identified the following milestones in our Credit Card Dispute case:

1. Dispute started
2. Chargeback approved
3. Dispute rejected

These are the achievable targets to help us track the progress of the dispute. They don't have any particular order, so you can come back to any of them if something in the Case File data changes.

Next you will learn how to model Milestones inside your Case Definition.

NOTE: _If you found any issues and you prefer to import a project with the previous steps completed, delete your `ccd-project` project and re-import it using this URL: [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-2.git](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-2.git)._

## Defining Milestones

To model the milestones of the case:

1. Open the `ChargeDispute` process.

2. Delete the default Milestone node from the diagram;

3. Select from the Object Library Panel a Node of type _Script Node_ (located in the _Tasks_ section of the palette). Place it on the canvas. On the properties panel for the _Script Node_ enter the following:

  | Name            | Value     |
  | --------------- |:-------------:|
  | Name  | Log Case Started |
  | Script  | `System.out.println("Case started");` |
  | AdHoc Autostart  | True |

  ![Business Central Designer Script Task Properties]({% image_path ccd-project-log-case-started-node-properties.png %}){:width="600px"}

  Note that we have not defined a _Start_ node in our case. By enabling the `Adhoc autostart` on the script node, the node will be activated and executed automatically when the case is started.

  ![Business Central Designer Script Task]({% image_path ccd-project-log-case-started-node.png %}){:width="600px"}

4. Add an _End Event_ of type _End Signal_ and set the signal name to _Dispute_Received_, so once you've completed the logging that the case has started the signal will trigger a Milestone called `Dispute_Received`.

  | Name            | Value     |
  | --------------- |:-------------:|
  | Signal  | Dispute_Received |
  | Signal Scope  | Process Instance |

  ![Business Central Designer Script Task End Event]({% image_path ccd-project-end-signal-dispute_received.png %}){:width="600px"}

  

5. Add a _Milestone_ node. Set its name to _Dispute_Received_. Bear in mind that for the _Milestone_ to be triggered by the _Signal End Event_ we created earlier, the _Signal Ref_ of the event should have the exact same name as the _Name_ of the _Milestone_ (which we've done - both are _Dispute Received_). We set the condition to trigger when the `fraudData` case file item is not `null`. _Note that triggering the Milestone only activates it, it does not complete it. A Milestone is completed when its Condition is met._
You set the `Condition` of a _Milestone_ in the _Assignments_ properties of the _Milestone_ node. Simply select the node, and click on the `Assignments` field of the property editor (the panel on the right side of the screen). This will open the _Data Input/Output Assignments_ editor. The data-input `Condition` should already be listed. In the _Source_ field, select `Constant`, and type (or paste) the condition expression.

  | Name            | Value     |
  | --------------- |:-------------:|
  | Name  | Dispute received |
  | Condition  | CaseData(data.get("fraudData") != null) |
  | Adhoc autostart | false |

  ![Business Central Designer Milestone Dispute Assignments]({% image_path milestone-input-condition.png %}){:width="600px"}

  It will look like this:

  ![Business Central Designer Milestone Dispute Received]({% image_path ccd-project-milestone-dispute_received.png%}){:width="600px"}

  As we saw earlier with our script task, ad hoc nodes with no incoming connections, like the _Milestone_ we've just defined, can be configured with the `Adhoc autostart` property, which is a property of the node itself. This will activate the node automatically when the case is started. Another way of triggering/activating an _ad hoc_ node is by signalling it. In this case our _Signal End Event_ triggers our _Milestone_ node and activate it. Remember that milestone activation does not complete it. A milestone is completed when it's completion condition is met. In another words, completion of a milestone is driven by conditional expressions on the state of the data. It is data-driven.

6. Save your process/case definition.

Now let's test this case.

## Tracking case instance within Business Central

Business Central offers a monitor view that shows details about each process instance and tasks, and allows also to interact with these items like:

- Starting, aborting, inspecting processes,
- Listing, vizualizing, and managing human tasks (claim, start, etc..);
- Check the logs of each step of the process.

Let's use Business Central, which is managing and monitoring the Process Engine (a.k.a Kie Server), to see more details about the case instance you started.

Go back to your project's _Asset Library_ view. Click on the _Deploy_ button in the upper right corner of the screen. This will package and deploy your project to the Execution Server. The workbench will display 2 green notification bars, stating the build and deployment were successful.

1. In Business Central, go to _Menu -> Manage -> Process Definitions_. You will see you `ChargeDispute` listed.

    ![Business Central Process Definitions Charge Dispute]({% image_path business-central-process-definitions-chargedispute.png %}){:width="600px"}

    **Note**: As part of the pre-defined project, we've already created the Case Start Form, which will allow you to start the case and provide its input data.

2. Click on the kebab icon on the right side of the process/case definition. Click on _Start_ to start the case. This will open the case start form that was provided by us, and which allows you to enter the data to your first case instance.

    ![Start Charge Dispute Case]({% image_path start-chargedispute-case.png %}){:width="600px"}

3. The start form allows us to specify the mappings for the case roles and groups, as well as the Credit Card Dispute input data for our case. Fill in the following details (make sure to set the `owner` to `pamAdmin`. _Failing to do so will prevent the case from being displayed in our case app for your `pamAdmin` user)._

    ![Start Charge Dispute Case Form 1]({% image_path start-chargedispute-case-form-1.png %}){:width="600px"}

    ![Start Charge Dispute Case Form 2]({% image_path start-chargedispute-case-form-2.png %}){:width="600px"}

    ![Start Charge Dispute Case Form 3]({% image_path start-chargedispute-case-form-3.png %}){:width="600px"}

4.  When you've filled in the input data, click on the _Submit_ button, which will start your case. The screen will automatically change to the _Process Instance Details_ screen of the process/case you've just started.

5. Click on the _Diagram_ tab to see the diagram of your case, and the nodes that have been traversed in your case instance. Note that the _Milestone_ is colored grey, what means that it was both triggered (by the _Signal End Event_), as well as completed (by the _Condition_ that evaluated to `true`).

    ![Start Charge Dispute Diagram]({% image_path start-chargedispute-diagram.png %}){:width="600px"}

10. Click on the _Process Variables_ to get an overview of the current state /values of the Case. Note that `fraudData` is indeed set, which was the _Condition_ that completed our _Milestone_.

    ![Start Charge Dispute Case Variables]({% image_path start-chargedispute-case-variables.png %}){:width="600px"}

1. Access your busines central;

2. Under `Menu` -> `Manage`, select `Process instances`. You should see as many cases as you created from the Showcase app.

3. Click on the process instance so you can see its details.

## Validating your case using Case Management Showcase App

The platform also provides a _Case Management Showcase_ application, which is an **example** of how a custom Case Management dashboard could be composed. Let's test your process using it.

1. Open this application by clicking on the _Application Launcher_ button on the top right of the screen. Next, click on _Case Management Showcase_.

    ![Business Central Case Management Showcase Button]({% image_path business-central-cm-showcase-button.png %}){:width="600px"}

2. Login with the same credentials that you used for the Business Central Workbench (u:`pamAdmin`, p:`redhatpam1!`).

5. The showcase application will list the new opened case. You will see the case you've just started in the list:

    ![Case Management Showcase ChargeDispute List]({% image_path cms-chargedispute-case-list.png %}){:width="600px"}

4. Click on the case to open a detailed view of the open case. This view will, among other things, list the completed milestones, the available actions, the actions that are in progress, the completed actions and the case roles. Take some time to explore the details of your case.

    ![Case Management Showcase ChargeDispute Details]({% image_path cms-chargedispute-case-details.png %}){:width="600px"}

# Conclusion

So far we've configured the initial process, a `signal end-event` that triggers the `milestone`, and a `milestone` conditional expression which fired based on the `case data`.

In the next section we will add our rules, decisions and user-tasks to our project.
