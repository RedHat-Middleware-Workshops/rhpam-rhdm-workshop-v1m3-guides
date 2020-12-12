# 8. Using Automated Decisions within your flows

In this section you will learn how to configure different types of decisions so you can integrate them with cases or processes.

_This section is mandatory for those who are using the projects created on "Module 2 - Authoring Business Rules and Decisions with Red Hat PAM". If you this is not your case, the steps here are already implemented in your project._

## Automated decisions in this project

When you start a credit card dispute case you can go through two different scenarios: automated chargeback or standard processing. 

In this project, the rules that support this decision making are automated in two assets: a Guided Rule named "automated-chargeback" and a Guided Decision Table name "risk-evaluation". 

In order to enable our processes and cases to consume these rules, we should use an extra property called `ruleflow-group`. This property is an attribute that can combine a number of rules into a group, after which the execution of those rules can be controlled by activating the `ruleflow-group` from the process/case via a decision node.

Let's configure our rules so we can use them from within our case. 

## Adding the ruleflow-group configuration to the Guided Rule

1. In the Business Central assets list, open the `automated-chargeback`
2. Click on the `Show options...` label
  ![Business Central Guided Rule Automated Chargeback]({% image_path automated-chargeback-guided-rule-more-options.png %}){:width="800px"}

3. Next, add a new option by clicking the `plus` icon:
  ![Business Central Guided Rule Automated Chargeback New Option]({% image_path automated-chargeback-guided-rule-new-options.png %}){:width="800px"}

4. In the pop-up, set the `attribute` as `ruleflow-group`

5. You should see a new field for the `ruleflow-group`. Fill it with the value `automated-chargeback`
   ![Business Central Guided Rule Automated Chargeback New Option]({% image_path automated-chargeback-guided-rule-configured.png %}){:width="800px"}

6. Save the business rule.

## Adding the ruleflow-group to the Guided Decision Table

1. In the Business Central assets list, open the `risk-evaluation` Guided Decision Table
2. Click on the `Columns` tab
  ![Business Central GDST Column tab]({% image_path gdst-column.png %}){:width="600px"}
3. Next, click the `Insert Column` button. Select the checkbox `Include advanced options`
4. New options will show on the menu. Select the `Add an Attribute column` option, and click next
  ![Business Central GDST RuleFlow Group Option]({% image_path gdst-adv-options.png %}){:width="600px"}

5. Select `Ruleflow-Group`, and click `Finish`;
  ![Business Central GDST RuleFlow Group Option]({% image_path gdst-new-option.png %}){:width="600px"}

6. Still on the column tab, open the `Attributes Column` menu. Insert the ruleflow-group name: `automatic-approval`, and check the `Hide Column` check box.
  ![Business Central GDST Ruleflow Group Config]({% image_path gdst-ruleflow-config.png %}){:width="600px"}

## Add a new DRL Rule

We will need a rule to define if a chargeback can be automatically made, or if it needs to be manually approved. This rule has a simple logic, but it demonstrates how you can make automatic decision made on case file and manipulate it when needed. 

1. In the Business Central, add a new asset of type `DRL File`
2. Name it `automatic-approval`
3. Use the following code in your rule:

~~~ 
package com.myspace.ccd_project;

import com.myspace.ccd_project.FraudData;
import org.kie.api.runtime.process.CaseData;

rule "automatic-approval"
ruleflow-group "automatic-approval"
no-loop
when
    $caseFile: CaseData()
    $fraudData: FraudData(automated == true) from $caseFile.getData("fraudData")
then
    System.out.println("Automatically approving chargeback.");
    modify($caseFile) {
        add("approvedChargeback", true);
    }
end
~~~

Note our ruleflow-group is set to `automatic-approval`, and that the `then` clause is modifying our FraudData object existing in our caseFile, setting the attribute `approvedChargeback` to `true`.


----- 

With this, we have configured all the rules and into three different ruleflow groups. You can confirm that by switching to the `Source` tab and evaluating the rules code.

Now, let's go ahead and use these rules in our `ChargeDispute` case.


