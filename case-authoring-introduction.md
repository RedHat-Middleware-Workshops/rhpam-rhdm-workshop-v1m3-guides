
# First steps to author a case.

In this section you will learn:

1. Requisites to author your first case.

2. What are roles and variables.


## Overview
You are in charge of automating the business process to solve a dispute. Solving a credit card dispute depends on several variables, like  the type of customer, his/her status, the amount of the dispute etc. The knowledge of how to apply these rules and decisions is tacit, and lives only in the head of other domain experts like you. In order to automate the process you have to first explicitly define the various steps and structure of the process.

What happens when a Credit Card Holder starts a dispute?

A Credit Card Dispute process is not a straightforward process, nor is it fully structured. You usually don't start at point A and then follow the path to point B, all the way through the process in a fully pre-defined, structured, flow. What happens is that, depending on the decisions made during the execution the of process instance, and most importantly the data of the case, you will jump back and forth between different steps to solve the dispute.
There are several _actors_ involved in the dispute.

_NOTE: Review the use case overview (step 1) where all the Actors are listed and defined._


As we saw earlier, since this is a dynamic, unstructured, and data-driven process, the best way to model it is as a _case_. In this specific _case_ we have a goal, i.e. to solve the dispute, but the steps that will lead to this result do not follow an explicitly structured flow.

The _Issuer_ will gather information from the Credit Card Holder and the merchant to store it in the _Case File_. The _Case File_ is a collection of case data, or case file items, that is defined and stored on case-instance level. I.e. all activities, sub-process, stages and process fragments have access to a single collection of data for that specific case instance, the Case File. This Case File can be accessed by all of the actors at any time, but you can also define Role Based Access Controls to protect the information.

Case management planning is typically concerned with determination of which Tasks are applicable, or which follow-up Tasks are required, given the state of the Case. Cases are directed not just by explicit knowledge about the particular Case and its context represented in the Case File, but also by explicit knowledge encoded as rules by business analysts, the tacit knowledge of human participants, and tacit knowledge from the organization or community in which participants are members.

NOTE: In real life the _Issuer_ would deal with the Credit Card Processor and not the merchant directly, but for the sake of simplicity we will just take the merchant into account.

![Business Central CC Dispute Diagram Users]({% image_path business-central-cc-dispute-diagram-users.png %}){:width="600px"}

## The Modeler

To model cases and processes, the Business Central workbench provides a web-based BPMN2 designer. Let's explore it in more detail.

![Business Central Designer Explained]({% image_path business-central-designer-explained.png %}){:width="600px"}

1. _Modelling Canvas_: This is your process drawing board. After dropping different shapes onto the canvas, you can move them around, connect them, etc. Clicking on a shape on the canvas allows you to set its properties in the expandable Properties Window (3) (as well as create connecting shapes and morph the shape into other shapes).

2. _Toolbar_: The toolbar contains a vast number of functions offered by Designer (described later). These includes operations that can be performed on shapes present on the Canvas. Individual operations are disabled or enabled depending on what is selected. For example, if no shapes are selected, the Cut/Paste/Delete operations are disabled, and become enabled once you select a shape. Hovering over the icons in the Toolbar displays the description text of the operation.

3.  _Properties Panel_: This expandable section on the right side of Designer allows you to set both process and shape properties. It is divided into four sections, namely "Core properties", "Extra Properties, "Graphical Settings" and "Simulation Properties". After clicking on a shape in the Canvas, this panel is reloaded to show properties specific to that shape type. If you click on the canvas itself (not on a shape) the panel displays general process properties.

4.  _Object Library Panel_: The expandable section on the left side of Designer shows the jBPM BPMN2 (default) shape repository tree. It includes all shapes of the jBPM BPMN2 stencil set which can be used to assemble your processes. If you expand each section sub-group you can see the BPMN2 elements that can be placed onto the Designer Canvas (1) by dragging and dropping the shape onto it.

5. _View Tabs_: Currently, Designer offers three tabs for Process Modelling, Simulation and Documentation. Process Modelling is the default tab. When users run process simulation, its results are presented in the Simulation tab. Process Documentation tab displays documentation generated from process definition.

6. _Info Tabs_: There are two additional tabs at the top of Designer. The _Editor_ tab includes the process modeling, while the _Overview_ tab displays the process metadata such as version history, creation date, last modification date etc.


## Case Variables and Roles

We have defined the _Business Object Model_ and the _Business Decisions_ in the previous lab. If you've completed the labs in the previous steps, you can use your existing project. You can also import the project from this location in your workspace: [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1)

You can watch the video, that was provided earlier in the workshop, on how to import a repository into your workspace.

1. Open the [OpenShift Console]({{ OPENSHIFT_CONSOLE_URL }}) in a web-browser. You've been assigned a username and password by your lab instructor. Use these credentials to login to the console.

2. In your OpenShift Console, you will see a project called "RHPAM - User {x}". Click on this project to open it.

3. In this project you will see an application called `rhpam7-rhpamcentr`. Click on the box of this application to expand it. On the lower right-hand-side of this box you will see a section called `Routes - External Traffic`. Click on either the
`http` or `https` route to access the RHPAM Business Central Workbench.

    ![OpenShift Business Central Route]({% image_path openshift-business-central-route.png %}){:width="600px"}

4. Login to Business Central with the credentials u:`pamAdmin`, p:`redhatpam1!`

    ![Business Central Console]({% image_path business-central-console.png %}){:width="600px"}

5. Import the base project for this scenario from the following repository: [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1)


### Creating your first Case Definition

To create your first Case Definition:

1. Go to your library view and click on _Add Asset_. From the asset catalog select Case Definition (legacy), configure the following values:

Name: `ChargeDispute`
Package: `com.myspace.ccd_project`

![Business Central Asset Business Legacy]({% image_path business-central-asset-business-legacy.png %}){:width="600px"}

#### Defining Case Variables

We will first define our case variable. These variables will be used to store the case data during the execution the case. My setting the _Case File_ boolean of the variable to true, we configure the variable to be a _Case File Item_, stored in the _Case File_.

1. On the properties panel, In the Variable Definitions property field, click on the v icon to open the variable definitions editor, add the following values

Name:  `customerStatus`

    Defined Types: String

    Custom Type:

    Case File: true

    KPI: false

Name:  `totalFraudAmount`

    Defined Types: Float

    Custom Type:

    Case File: true

    KPI: false

Name:  `fraudData`

    Defined Types: FraudData

    Custom Type:

    Case File: true

    KPI: false

Name:  `approveChargeback`

    Defined Types: Boolean

    Custom Type:

    Case File: true

    KPI: false


Name:  `cardholder`

    Defined Types: CreditCardHolder

    Custom Type:

    Case File: true

    KPI: false


At the end your variable definitions should look like this:

![Business Central Designer Variables]({% image_path business-central-designer-variables.png %}){:width="600px"}


#### Defining Roles

In the Credit Card Dispute case, we can identify different roles. The mapping of the users and/or groups to these case roles is done when the case instance is started, and can be changed afterwards.


1. On the properties panel look for the Case Roles option and click on the V icon to the right.

![Business Central Designer Roles]({% image_path business-central-designer-roles.png %}){:width="600px"}

2. Add the roles the following roles:

Case Role: `owner`
Case Cardinality: 1


Case Role: `approval-manager`
Case Cardinality: 1

The cardinality refers to the number of actors that can be mapped to a role.

You have completed the setup of your case by defining the variables and the roles. The full definition can be found in the following repo: [https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1](https://github.com/RedHat-Middleware-Workshops/rhpam-rhdm-workshop-v1m3-labs-step-1)

---
**NOTE**

To be able to import this project, you will have to delete your current project first and then import the repository.

---

In the project we've just imported we've defined a number of additional case variables. Make sure to examine the rest of the variables that were created for you.
