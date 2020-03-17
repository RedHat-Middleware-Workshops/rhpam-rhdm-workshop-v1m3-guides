
# Decisions Integration.

In this section you will learn:

1. How to reuse the Decision making assets inside a Case Management definition.

2. How to configure and use a Business Rule Node inside a Case Definition.


## The Credit Card dispute case

![Business Central CC Dispute Diagram Users]({% image_path business-central-cc-dispute-diagram-users.png %}){:width="600px"}

There are several thing that could happen when you dispute a case, we will see 2 different scenarios

### Automated Chargeback

A credit card dispute over billing errors has a good chance of being resolved in your favour thanks to the Fair Credit Billing Act, which regulates how credit card companies handle these disputes.  The amount of the transaction, or your status as a customer can also qualify you for an automated chargeback.

The process would look like as follows:

1. CC Holder starts the dispute.

2. The information of the case is automatically evaluated by business rules and the decision of an automated chargeback is taken.

3. The issuer of the Credit Card (CC) will credit the disputed amount into your account.


### Using Business Decisions in a Case

<!-- In the previous step we've defined the first  _Milestone_ of the case. Let's re-import a more complete version of the project to start this exercise from:

1. Delete the current project

    1. At the top of the screen under the main heading, click the _ccd-project_ to bring you back to the homepage for the project

    ![Business Central Breadcrumb bar ccd project]({% image_path business-central-breadcrumb-bar-ccd-project.png %}){:width="600px"}

    2. Delete the project by clicking the hamburger menu & selecting _Delete Project_

    ![Business Central Delete CCD Project]({% image_path business-central-delete-ccd-project.png %}){:width="600px"}

    3. Type in _ccd-project_ and click `Delete Project`
    4. If asked you can `Discard unsaved changed and proceed`

2. Import the project
    1. Click the `Import Project` button
    2. Enter https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-3.git as the _Repository URL_ and click `Import`
    3. On the _Import Projects_ screen, select the _ccd-project_ and click `Ok`

    ![Business Central Delete CCD Project]({% image_path business-central-import-ccd-project.png %}){:width="600px"}
 -->

To be able to decide the type of processing of the Credit Card Dispute we need to use the rules for automatic chargeback processing that we automated in the previous scenario. The rules look like this:

![Business Central Guided Rule Modify Fraud Automated True]({% image_path business-central-guided-rule-modify-fraud-automated-true.png %}){:width="600px"}

If you open the asset, you will notice that we've added an extra property called `ruleflow-group`. This property is an attribute that can combine a number of rules into a group, after which the execution of those rules can be controlled by activating the `ruleflow-group` from the process/case via a decision node.

The evaluation to decide if a chargeback should be automatic is the first step after the dispute is started, so we are going to add the step right after the `Dispute received` is triggered. _Remember that Milestones don't perform any actions, they mark a target of the case as achieved. However, functionality can be linked to these Milestone nodes. These nodes will start after the Milestone is completed._ Â

1. Add a node of type `Business Rule` linked after the `Dispute received` milestone node (the `Business Rule` node can be found in the `Tasks` section of the palette).

2. With the `Business Rule` selected, in the properties panel add the following information:
  - Name:  `Check for automated chargeback`    
  - Task Type: Business Rule  
  - Rule Flow Group: `automated-chargeback`

3. For the rule to evaluate the facts, we need to correctly insert the facts into the rule-engine. We can do this via the nodes Input/Output Data mapping. If we specify a process variable or case file item in the Data Input section, that data will be inserted as a fact into the rules engine's so called _Working Memory_. If we specify the same fact, with the same names, in the Data Output section, the fact will, after rule-evaluation, also be retracted from _Working Memory_. This last part is important when you have multiple Business Rules nodes in your process and/or case definition, and you want to be sure that the rules are only evaluating the data that you're entering in that specific node. Select the `Business Rule` node we've just created, and in the properties panel click on the _Assignments_ property to open the Data Input Output editor.

3. The automated chargeback rule evaluates the `CreditCardHolder` and the `FraudData`. We therefore need to insert these 2 _case file items_ directly into the Working Memory of the engine. Configure the Data Input/Output mapping of your business rule node as follows:

  Data Input Assignments:

  | Name            | Data Type     | Source       |
  |:---------------|:-------------|:-------------|
  | brCreditCardHolder  | CreditCardHolder |caseFile_creditCardHolder |
  | brFraudData | FraudData  | caseFile_fraudData |

  Data Output Assignments:

  | Name            | Data Type     | Source       |
  |:---------------|:-------------|:-------------|
  | brCreditCardHolder  | CreditCardHolder |caseFile_creditCardHolder |
  | brFraudData | FraudData  | caseFile_fraudData |

  ![Business Central Case First Business Rule Node]({% image_path business-central-case-first-business-rule-node.png %}){:width="600px"}

## Using gateways

  As part of the rule's action (the right-hand-side, or consequence, of the rule), the case data might change. For example, when the dispute is eligible for automated chargeback, the rule will change the `FraudData` fact/case file item by setting its `automated` property to `true`. Hence, we want to use a conditional gateway to decide whether we can do automatic approval or not.

1. Add a `X-Or Gateway` after your `Check for automated chargeback` Business Rule node.

    ![Business Central Case X-OR Gateway]({% image_path business-central-case-xor-gateway.png %}){:width="600px"}


2. Create 2 new script tasks, one called `Automatic Approval` and the other called `Manual Approval`. These serve only as placeholders, so they do not need to have a script implementation. Connect the 2 tasks to the X-OR gateway.

![Business Central Case X-OR Gateway Tasks]({% image_path business-central-case-xor-gateway-tasks.png %}){:width="600px"}

3. Give one of the _Sequence Flows_ (the arrows connecting the nodes) the name `automatic` and the other the name `manual`. Not only is naming the sequence flows after a gateway a good practice, it also helps when selecting the default flow in the next step of this lab.

4. We can now implement the conditional expressions on the _X-OR Gateway_ and the _Sequence Flows_ connecting the gateway to the task nodes. We're first going to define the _Default gate_ of the gateway. I.e. the path that should be taken when no conditions are met. Select the _X_OR Gateway_ node, open the properties panel on the right side of the editor and set the `Default gate` property to the `manual` flow.

    ![Business Central Case X-OR Gateway Default Gate]({% image_path business-central-case-xor-gateway-default-gate.png %}){:width="600px"}

4. Select the `automatic` _Sequence Flow_. In the properties panel, click on `Implementation/Execution` and select the `Expression` radio button.

5. Set the following expression in the _Script_ tab of the expression editor:

  Expression: `return (caseFile_fraudData.getAutomated()) != null && (caseFile_fraudData.getAutomated() == true);`

  The expression will activate when the `automated` field of the `FraudData` has been set (not `null`) and the value is `true`.

  ![Business Central Sequence Flow Expression]({% image_path business-central-sequence-flow-expression.png %}){:width="600px"}

6. Save the case definition.

## Testing the case definition

Let's try the business decision and nodes within the case we just updated.

1.  and deploy the project to the execution server by clicking on the _Deploy_ button from the _Asset Library_ perspective.

2. Start a new case instance like we did in the previous lab. Use the same input data. Open the diagram of the Case/Process Instance and note that the dispute you've entered requires manual approval.

  ![Case With Placeholders Manual Approval]({% image_path case-with-placeholders-manual-approval.png %}){:width="600px"}

3. Start a new case instance, but this time set the Credit Card Holder's status to `Gold`. This should cause the rules to make the dispute eligible for automatic processing. Open the diagram of the Case/Process Instance and observe that the case has indeed taken the path of automatic processing.

    ![Case With Placeholders Automatic Approval]({% image_path case-with-placeholders-automatic-approval.png %}){:width="600px"}

You have just learned how to leverage the Decisions and Rules you authored in the previous scenario in your case definition. You have seen how the state of the data, in this case the Card Holder's status, triggers rules. You've seen how the rules manipulate the state of the data, in this case setting the `automatic` field of the `FraudData` to `true`, which can drive decisions and flow directions within our case.

Apart from changing data, decisions and rules can also infer and create new data, as well as remove data from the case instance. Through decisions and rules, the data-driven approach of Case Management (in contrast to the flow driven approach of traditional BPM) allows for the implementation of very dynamic, data-driven, case logic.
