
# Decisions Integration.


In this section you will learn:

1. How to reuse the Decision making assets inside a Case Management definition.

2. How to configure and use a Business Rule Node inside a Case Management Definition.


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

In the previous step we've defined the first  _Milestone_ of the case. Import the project from the following repository:

[https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-3](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-3)

To be able to decide the type of processing of the Credit Card Dispute we need to apply the rules for automatic chargeback processing that we automated in the previous scenario. The rules look like this:

![Business Central Guided Rule Modify Fraud Automated True]({% image_path business-central-guided-rule-modify-fraud-automated-true.png %}){:width="600px"}

If you open the asset, you will notice that we've added an extra property called `ruleflow-group`. This property is an attribute that can combine a number of rules into a group, after which the execution of those rules can be controlled by activating the `ruleflow-group` from the process/case via a decision node.

The evaluation to decide if a chargeback should be automatic is the first step after the dispute is started, so we are going to add the step right after the `Dispute received` is triggered. Remember that _Milestones_ don't perform any actions, they mark a target of the case as achieved. However, functionality can be linked to these _Milestone_ nodes. These nodes will start after the _Milestone_ is completed.

1. Add a node of type `Business Rule` to the `Dispute received` milestone node (the `Business Rule` node can be found in the `Tasks` section of the palette). In the properties panel add the following information:

    Name:  `Check for automated chargeback`
    Task Type: Business Rule  
    Rule Flow Group: `automated-chargeback`

2. For the rule to evaluate the facts, we need to correctly insert the facts into the rule-engine. We can do this via the nodes Input/Output Data mapping. If we specify a process variable or case file item in the Data Input section, that data will be inserted as a fact into the rules engine's so called _Working Memory_. If we specify the same fact, with the same names, in the Data Output section, the fact will, after rule-evaluation, also be retracted from _Working Memory_. This last part is important when you have multiple Business Rules nodes in your process and/or case definition, and you want to be sure that the rules are only evaluating the data that you're entering in that specific node. Select the `Business Rule` node we've just created, and in the properties panel click on the _Assignments_ property to open the Data Input Output editor.

3. The automated chargeback rule evaluates the `CreditCardHolder` and the `FraudDate`. We therefore need to insert these 2 _case file items_ directly into the Working Memory of the engine. Configure the Data Input/Output mapping of your business rule node as follows:

    ![Business Central Case First Business Rule Node]({% image_path business-central-case-first-business-rule-node.png %}){:width="600px"}

    As part of the rule's action (the right-hand-side, or consequence, of the rule), the case data might change. For example, when the dispute is eligible for automated chargeback, the rule will change the `FraudData` fact/case file item by setting its `automated` property to `true`. Hence, we wan to use a conditional gateway to decide whether we can do automatic approval or not.


1. Add a `X-Or Gateway` after your `Check for automated chargeback` Business Rule node.

    ![Business Central Case X-OR Gateway]({% image_path business-central-case-xor-gateway.png %}){:width="600px"}


2. Create 2 new script tasks, one called `Automatic Approval` and the other called `Manual Approval`. These serve only as placeholders, so they do not need to have a script implementation. Connect the 2 tasks to the X-OR gateway. Give one of the _Sequence Flows_ (the arrows connecting the nodes) the name `automatic` and the other the name `manual`. Not only is naming the sequence flows after a gateway a good practice, it also helps when selecting the default flow in the next step of this lab.

    ![Business Central Case X-OR Gateway Tasks]({% image_path business-central-case-xor-gateway-tasks.png %}){:width="600px"}


3. We can now implement the conditional expressions on the _X-OR Gateway_ and the _Sequence Flows_ connecting the gateway to the task nodes. We're first going to define the _Default gate_ of the gateway. I.e. the path that should be taken when no conditions are met. Select the _X_OR Gateway_ node, open the properties panel on the right side of the editor and set the `Default gate` property to the `manual` flow.

    ![Business Central Case X-OR Gateway Default Gate]({% image_path business-central-case-xor-gateway-default-gate.png %}){:width="600px"}

4. Select the `automatic` _Sequence Flow_. In the properties panel, set the following expression in the _Script`_ tab of the expression editor:

    Expression: `return (caseFile_fraudData.getAutomated()) != null && (caseFile_fraudData.getAutomated() == true) ;`

    The expression will activate when the `automated` field of the `FraudData` has been set (not `null`) and the value is `true`.

    ![Business Central Sequence Flow Expression]({% image_path business-central-sequence-flow-expression.png %}){:width="600px"}

    ![Business Central Case X-OR Gateway Expression Editor]({% image_path business-central-sequence-flow-expression-editor.png %}){:width="600px"}

5. Save the case definition and deploy the project to the execution server by clicking on the _Deploy_ button from the _Asset Library_ perspective.

6. Start a new case instance like we did in the previous lab. Use the same input data. Open the diagram of the Case/Process Instance and note that the dispute you've entered requires manual approval.

7. Start a new case instance, but this time set the Credit Card Holder's status to `Gold`. This should cause the rules to make the dispute eligible for automatic processing. Open the diagram of the Case/Process Instance and observe that the case has indeed taken the path of automatic processing.







You have just learned how to leverage the Decisions and Rules you author in the previous scenario, when a new case is started you will receive the data to process the Dispute. When you reach a Business Rule node in the Case Model this data stored in the Case variables. The variables can be of primitive type or reference the Object Model will be passed to the rules.

The evaluation of the rules can produce more data or modify the existing one, and all these will be stored in the case variables.


### Adding the Automatic Chargeback functionality

For specific Credit Card Holders when the amount is below a certain threshold, the Issuer will automatically accredit back the dispute amount into the account, without any further processing.

In our model we have Milestone that marks thar the CC Holder has achieved that target, if you recall from the last scenario the Milestone is triggered by a condition. The Milestone is triggered when the property automated in the Object FraudData is set to true.
The Rules for automatic-chargeback modify this property and set it to true if the conditions are met.

1- Open the CreditDispute Case and examine in the properties panel the condition to trigger the execution. The condition is declared in assignements> Data Inputs and Assignments> Condition. The condition is set as a constant value.

![Business Central Case FAutomated Chargeback Condition]({% image_path business-central-case-fautomated-chargeback-condition.png %}){:width="600px"}

The last step to finish the dispute is to accredit the fraud amount back to the account, we will add the functionality and then trigger the Milestone: Account credited

2- Add a node of type Script to the Milestone 3: Automated Chargeback Processing, input the following information to the Node in the properties panel.


Name:  `Accredit the Fraud Amount to CC  Holder`{{copy}}  
Task Type: Script  
Script:`System.out.println("Amount accredited into the account");`{{copy}}  

Here we are simulating the call to an external service, that will actually accredit the account.

![Business Central Case Automated Chargeback Script]({% image_path business-central-case-automated-chargeback-script.png %}){:width="600px"}

Finally since the account was accredited due to the automatic processing we will trigger the Milestone 4: Account Acredited

3- Add and End Event of type signal after the Script node and input the following information.

Name:  `Account Acredited`{{copy}}  
SignalRef: `Milestone 4: Account Acredited`{{copy}}  

![Business Central Case Automated Chargeback End Event]({% image_path business-central-case-automated-chargeback-end-event.png %}){:width="600px"}

4- Click save.

You have just integrated rules in your case, to modify the execution flow by triggering Milestones.
It's important to remember that the Case instance is constantly listening to changes in the Object Model or case variables if they are marked as CaseFile variables. If a variable changes, this will automatically trigger Milestones giving your case a Data driven type of execution.


To import the repository with the solution for this step, delete your current project and import:

https://github.com/MyriamFentanes/case-management-scenario-step5.git
