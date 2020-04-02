
# Using Automated Decisions

In this section you will learn:

1. How to reuse the Decision making assets inside a Case Management definition.

2. How to configure and use a Business Rule Node inside a Case Definition.

## The Credit Card dispute case

When you start a credit card dispute case you can go through two different scenarios: automated chargeback or standard processing. In this module, we should work on automating the first scenario where the chargeback can be automatically processed based on the defined business rules. Let's understand a little bit more about the automated chargeback process:

### Automated Chargeback

A credit card dispute over billing errors has a good chance of being resolved in the favor of customers. This is due to different data collected during the card usage. For instance, the amount of the transaction, or the customer status can also qualify for an automated chargeback.

The process would look like as follows:

1. CC Holder starts the dispute.

2. The information of the case is automatically evaluated by business rules and the decision of an automated chargeback is taken.

3. The issuer of the Credit Card (CC) will credit the disputed amount into your account.

Now let's get back to our automation project.

To be able to decide the type of processing of the Credit Card Dispute we need to use the rules for automatic chargeback processing that we automated in the previous scenario.

NOTE: _If you found any issues and you prefer to import a project with the previous steps completed, delete your `ccd-project` project and re-import it using this URL: [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-3.git](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-3.git)._

If you open the asset, you will notice that we've added an extra property called `ruleflow-group`. This property is an attribute that can combine a number of rules into a group, after which the execution of those rules can be controlled by activating the `ruleflow-group` from the process/case via a decision node.

1. If you want to check this rule in your environment, search for the `automated-chargeback` guided rule:

![Business Central Guided Rule Automated Chargeback]({% image_path automated-chargeback-guided-rule-asset-list.png %}){:width="600px"}

2. Click on `(show options)` and notice the `ruleflow-group` configured in your rule:

![Business Central Guided Rule Automated Chargeback Ruleflow Group]({% image_path automated-chargeback-guided-rule-ruleflow-group.png %}){:width="600px"}

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

The evaluation to decide if a chargeback should be automatic is the first step after the dispute is started, so we are going to add the step right after the `Dispute received` is triggered. _Remember that Milestones don't perform any actions, they mark a target of the case as achieved. However, functionality can be linked to these Milestone nodes. These nodes will start after the Milestone is completed._

1. Open the _ChargeDispute_ asset of _ccd-project_ (which you worked on earlier)

2. Add a node of type `Business Rule` (accessible under the _Activities_ tooltip on the palette on the left). Drag a linking arrow from the Milestone _Dispute Received_ milestone node to this new `Business Rule`.

3. With the `Business Rule` selected, in the properties panel add the following information:

  - Name:  `Check for automated chargeback`    
  - Task Type: Business Rule  
  - Rule Flow Group: `automated-chargeback`

4. For the rule to evaluate the facts, we need to correctly insert the facts into the rule-engine. We can do this via the nodes Input/Output Data mapping. If we specify a process variable or case file item in the Data Input section, that data will be inserted as a fact into the rules engine's so called _Working Memory_. If we specify the same fact, with the same names, in the Data Output section, the fact will, after rule-evaluation, also be retracted from _Working Memory_. This last part is important when you have multiple Business Rules nodes in your process and/or case definition, and you want to be sure that the rules are only evaluating the data that you're entering in that specific node. Select the `Business Rule` node we've just created, and in the properties panel click on the _Assignments_ property to open the Data Input Output editor.

5. The automated chargeback rule evaluates the `CreditCardHolder` and the `FraudData`. We therefore need to insert these 2 _case file items_ directly into the Working Memory of the engine. Configure the Data Input/Output mapping of your business rule node as follows:

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
