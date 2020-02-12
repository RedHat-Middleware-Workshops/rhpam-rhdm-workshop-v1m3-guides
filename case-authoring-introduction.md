
# Case Authoring - Introduction

In this section you will learn:

1. Requisites to author your first case.
2. What are roles and variables.


## Overview
The first step when automating a process is to explicitly define the various steps and structure of the process.

What happens when a Credit Card Holder starts a dispute? A Credit Card Dispute process is not a straightforward process. It usually don't start at point A and then follow the path to point B, all the way through the process in a fully pre-defined, structured, flow. What happens is that, depending on the *human decisions* made during the execution the of process instance, and most importantly the data of the case, it will jump back and forth between different steps and different _actors_ to solve the dispute.

_NOTE: Feel free to review the use case overview (step 1) where all the Actors are listed and defined._

Since this is a dynamic, unstructured, and data-driven process, the best way to model it is as a _case_.

In this specific _case_ we have a goal, i.e. to solve the dispute, but the steps and the order that will lead to this goal cannot be predicted. One of the steps we know is that the _Issuer_ will gather information from the Credit Card Holder and the Merchant to store it in the _Case File_.

_NOTE: In real life the _Issuer_ would deal with the Credit Card Processor and not the merchant directly, but for the sake of simplicity we will just take the merchant into account._

![Business Central CC Dispute Diagram Users]({% image_path business-central-cc-dispute-diagram-users.png %}){:width="600px"}

## The Modeler

To model cases and processes, the Business Central workbench provides a web-based BPMN2 designer. When creating a new asset you may notice there is the option to create a `Business Process` and a `Business Process (legacy)`. The difference in between both assets is that Business Central has a brand new process and case editor named Stunner. From version 7.5 on, the new process designer has a full-feature parity if compared to the legacy designer (which will be used in case you choose to create a new `Business Process (legacy).` asset).

 Let's explore the designers in more detail. Check both below:

This is how stunner looks like:
![Business Central Designer Explained - Stunner]({% image_path business-central-stunner-explained.png %}){:width="600px"}

And this is the legacy designer:
![Business Central (legacy) Designer Explained]({% image_path business-central-designer-explained.png %}){:width="600px"}

Let's understand a little bit more about each component:

1. _Modelling Canvas_: This is your process drawing board. After dropping different shapes onto the canvas, you can move them around, connect them, etc. Clicking on a shape on the canvas allows you to set its properties in the expandable Properties Window (3) (as well as create connecting shapes and morph the shape into other shapes).

2. _Toolbar_: The toolbar contains a vast number of functions offered by Designer (described later). These includes operations that can be performed on shapes present on the Canvas. Individual operations are disabled or enabled depending on what is selected. For example, if no shapes are selected, the Cut/Paste/Delete operations are disabled, and become enabled once you select a shape. Hovering over the icons in the Toolbar displays the description text of the operation.

3.  _Properties Panel_: This expandable section on the right side of Designer allows you to set both process and shape properties. It is divided into four sections, namely "Core properties", "Extra Properties, "Graphical Settings" and "Simulation Properties". After clicking on a shape in the Canvas, this panel is reloaded to show properties specific to that shape type. If you click on the canvas itself (not on a shape) the panel displays general process properties.

4.  _Object Library Panel_: The expandable section on the left side of Designer shows the jBPM BPMN2 (default) shape repository tree. It includes all shapes of the jBPM BPMN2 stencil set which can be used to assemble your processes. If you expand each section sub-group you can see the BPMN2 elements that can be placed onto the Designer Canvas (1) by dragging and dropping the shape onto it.

5. _View Tabs_: Currently, Designer offers three tabs for Process Modelling, Visualization and Documentation. Process Modelling is the default tab. Currently you can only work with process simulation with the legacy designer. When users run process simulation, its results are presented in the Simulation tab. Process Documentation tab displays documentation generated from process definition.

6. _Alerts Tabs_: There is a section at the bottom of Designer where business central shows alerts about process and code validation warnings and errors found during the project builds.


## Case Variables and Roles

We have defined the _Business Object Model_ and the _Business Decisions_ in the previous lab. If you've completed the labs in the previous steps, you can use your existing project.

NOTE: _If you'd prefer to start off fresh you can delete your `ccd-project` project and re-import it using this URL: [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1.git](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1.git)._

### Creating your first Case Definition

To create your first Case Definition:

1. Go to your library view and click on _Add Asset_. From the asset catalog select `Case Definition` (legacy), configure the following values:

Name: `ChargeDispute`
Package: `com.myspace.ccd_project`

![Business Central Asset New Case ]({% image_path ccd-add-case-definition.png %}){:width="600px"}

![Business Central New Case Details ]({% image_path ccd-project-add-case-definition-charge-dispute.png %}){:width="600px"}


#### Defining Case Variables

We will first define our case variable. These variables will be used to store the case data during the execution the case.

1. In the properties panel on the right-hand-side of the screen, scroll down to the bottom and expand the `Case Management` section. In the `Case File Variables` table, add the following variables:

| Name            | Data Type     |
| --------------- |:-------------:|
| customerStatus  | String |
| totalFraudAmount| Float  |
| fraudData | com.myspace.ccd_project.FraudData      |
| approvedChargeback | Boolean |
| creditCardholder | creditCardholder |


At the end your variable definitions should look like this:

![Business Central Variable Definitions]({% image_path case-file-variables.png %}){:width="600px"}

2. Save your asset by clicking on the `save` button.

#### Defining Roles

In the Credit Card Dispute case, we can identify different roles. The mapping of the users and/or groups to these case roles is done when the case instance is started, and can be changed afterwards.

1. On the properties panel look for the Case Roles table (just above the Case File Variables definition). Add the following roles:

| Name             | Cardinality |
| ---------------- |:-----------:|
| owner            | 1           |
| approval-manager | 1           |

The cardinality refers to the number of actors that can be mapped to a role.

![Business Central Case Roles]({% image_path case-roles.png %}){:width="600px"}

  <!--

  ## Import Remainder of Project

  You will now import the rest of the Case objects from a predefined repository.

  1. Delete the current project

      1. At the top of the screen under the main heading, click the _ccd-project_ to bring you back to the homepage for the project

      ![Business Central Breadcrumb bar ccd project]({% image_path business-central-breadcrumb-bar-ccd-project.png %}){:width="600px"}

      2. Delete the project by clicking the hamburger menu & selecting _Delete Project_

      ![Business Central Delete CCD Project]({% image_path business-central-delete-ccd-project.png %}){:width="600px"}

      3. Type in _ccd-project_ and click `Delete Project`
      4. If asked you can `Discard unsaved changed and proceed`

  2. Import the finished project
      1. Click the `Import Project` button
      2. Enter https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-2.git as the _Repository URL_ and click `Import`
      3. On the _Import Projects_ screen, select the _ccd-project_ and click `Ok`

      ![Business Central Delete CCD Project]({% image_path business-central-import-ccd-project.png %}){:width="600px"}

  3. In the project we've just imported we've defined a number of additional case variables. Make sure to examine the rest of the variables that were created for you. -->
