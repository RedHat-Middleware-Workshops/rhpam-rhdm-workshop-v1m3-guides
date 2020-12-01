
# 6. Using Automated Decisions

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

  ![Business Central Guided Rule Automated Chargeback]({% image_path automated-chargeback-guided-rule-asset-list.png %}){:width="800px"}

2. Click on `(show options)` and notice the `ruleflow-group` configured in your rule:

  ![Business Central Guided Rule Automated Chargeback Ruleflow Group]({% image_path automated-chargeback-guided-rule-ruleflow-group.png %}){:width="800px"}

### Using Business Decisions in a Case

The evaluation to decide if a chargeback should be automatic is the first step after the dispute is started, so we are going to add the step right after the `Dispute received` is triggered. _Remember that Milestones don't perform any actions, they mark a target of the case as achieved. However, functionality can be linked to these Milestone nodes. These nodes will start after the Milestone is completed._

1. Open the _ChargeDispute_ asset of _ccd-project_ (which you worked on earlier)

2. Click on the "Dispute Received" milestone, and add a new task: 

   ![Business Central Guided Rule Automated Chargeback Ruleflow Group]({% image_path dispute-milestone-new-task-node.png %}){:width="450px"}

3. Now, convert the new task to a `Business Rule` task (accessible under the _Activities_ tooltip on the palette on the left).

   ![Business Central Guided Rule Automated Chargeback Ruleflow Group]({% image_path convert-new-task-node-to-business-rules.png %}){:width="450px"}

4. Double click the rules task to change the name to `Check for automated chargeback`. 
   
5.  On the `Business Rule` properties on the right panel, update the ruleflow group values  "Implementation/Execution" section:

      - Rule Flow Group: `automated-chargeback`

6.   Save your work. Let's now send the case data to be evaluated against our rules.

7. The automated chargeback rule evaluates the `CreditCardHolder` and the `FraudData`. So, we need to insert these 2 _case file items_ in the `Working Memory` of the rules engine. Click on the "Assignments" option of your business rule:

	![Edit rules task data input/output]({% image_path edit-rules-task-data.png %}){:width="800px"}

1. Configure it as follow:

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

​	This is how it's gonna look like:

  ![Business Rules Task Assignment]({% image_path business-rules-task-data-assignment.png %}){:width="600px"}

9. Save your work. This is your case at this moment:

  ![Business Central Case First Business Rule Node]({% image_path business-central-case-first-business-rule-node.png %}){:width="600px"}



Now, let's change our automatic process based on our automatic decision making. 



## Using gateways

  As part of the rule's action (the right-hand-side, or consequence, of the rule), the case data might change. For example, when the dispute is eligible for automated chargeback, the rule will change the `FraudData` fact/case file item by setting its `automated` property to `true`. Hence, we want to use a conditional gateway to decide whether we can do automatic approval or not.

1. Select the `Check for automated chargeback` Business Rule node, and click on the gateway icon.

    ![Business Central Case X-OR Gateway]({% image_path business-central-case-xor-gateway.png %}){:width="600px"}

2. Now, convert the `parallel gateway` to an `exclusive gateway`:

    ![Convert Gateway]({% image_path convert-xor-gateway.png %}){:width="600px"}

3. Add a new task to the gateway, by selecting the `Create Task` option: 

    ![New Task on Gateway]({% image_path new-task-xor-gateway.png %}){:width="600px"}

4. Convert this task to a `Script Task`: 

    ![Convert Task to Script Task]({% image_path generic-task-to-script-task.png %}){:width="600px"}

5. Double click the `Script Task` and name it as  `Automatic Approval`. 
6. Now, follow the same steps, and add another `Script Task` connected to the gateway, and name is `Manual Approval`. 
7. These serve only as placeholders, so they do not need to have a script implementation. 

  ![Business Central Case X-OR Gateway Tasks]({% image_path business-central-case-xor-gateway-tasks.png %}){:width="800px"}

3. Give one of the _Sequence Flows_ (the arrows connecting the nodes) the name `automatic` and the other the name `manual`. Not only is naming the sequence flows after a gateway a good practice, it also helps when selecting the default flow on the next step.

4. We can now implement the conditional expressions on the _Exclusive  Gateway_ and the _Sequence Flows_ connecting the gateway to the task nodes. We're first going to define the _Default gate_ of the gateway. *I.e. the path that should be taken when no conditions are met*. Select the _Exclusive Gateway_ node, open the properties panel on the right side of the editor and set the `Default gate` property to the `manual` flow.

    ![Business Central Case X-OR Gateway Default Gate]({% image_path business-central-case-xor-gateway-default-gate.png %}){:width="800px"}

4. Select the `automatic` _Sequence Flow_. In the properties panel, click on `Implementation/Execution` and select the `Expression` radio button.

5. Set the following expression in the _Script_ tab of the expression editor:

  Expression: `return (caseFile_fraudData.getAutomated()) != null && (caseFile_fraudData.getAutomated() == true);`

  The expression will activate when the `automated` field of the `fraudData` has been set (is not `null`) and the value is `true`.

  ![Business Central Sequence Flow Expression]({% image_path business-central-sequence-flow-expression.png %}){:width="800px"}

6. Save the case definition.

## Testing the case definition

Let's try the business decision and nodes within the case we just updated.

1. Deploy the project to the execution server by clicking on the _Deploy_ button from the _Asset Library_ perspective.

2. Start a new case instance like we did in the previous lab, using the KIE Server Swagger UI. Use the same input data. 

      * containerId: `ccd-project`

      * case definition: `ccd-project.ChargeDispute` 

      * Body: 

        ````json
        {
          "case-data" : {
            "cardholder": {
                "CreditCardHolder": {
                  "age": 42,
                  "status": "Standard"
                }
            }, 
              "fraudData": {
                "FraudData": {
                  "totalFraudAmount": 49
                } 
              }
          },
          "case-user-assignments" : {
            "owner" : "pamAdmin",
            "approval-manager" : "pamAdmin"
          },
          "case-group-assignments" : { },
          "case-data-restrictions" : { }
        }
        ````

        ![Swagger UI Start Case 1]({% image_path swagger-ui-start-case-1.png %}){:width="800px"}

3. Back on Business Central, Open the diagram of the Case/Process Instance and note that the dispute you've entered requires manual approval.

  ![Case With Placeholders Manual Approval]({% image_path case-with-placeholders-manual-approval.png %}){:width="800px"}

3. Start a new case instance, but this time set the Credit Card Holder's status to `Gold`. 

      * containerId: `ccd-project`

      * case definition: `ccd-project.ChargeDispute` 

      * Body: 

        ````json
        {
          "case-data" : {
            "cardholder": {
                "CreditCardHolder": {
                  "age": 42,
                  "status": "Gold"
                }
            }, 
              "fraudData": {
                "FraudData": {
                  "totalFraudAmount": 49
                } 
              }
          },
          "case-user-assignments" : {
            "owner" : "pamAdmin",
            "approval-manager" : "pamAdmin"
          },
          "case-group-assignments" : { },
          "case-data-restrictions" : { }
        }
        ````


6. This should cause the rules to make the dispute eligible for automatic processing. Open the diagram of the Case/Process Instance and observe that the case has indeed taken the path of automatic processing.

  ![Case With Placeholders Automatic Approval]({% image_path case-with-placeholders-automatic-approval.png %}){:width="800px"}

You have just learned how to leverage the Decisions and Rules you authored in the previous scenario in your case definition. You have seen how the state of the data, in this case the Card Holder's status, triggers rules. You've seen how the rules manipulate the state of the data, in this case setting the `automatic` field of the `FraudData` to `true`, which can drive decisions and flow directions within our case.

Apart from changing data, decisions and rules can also infer and create new data, as well as remove data from the case instance. Through decisions and rules, the data-driven approach of Case Management (in contrast to the flow driven approach of traditional BPM) allows for the implementation of very dynamic, data-driven, case logic.
