# 7. Executing the Case Management application

In this section we'll test the case project we just authored. We will:

1. Deploy the project

2. Learn about the case management rest APIs

3. Track our case progress using Business central

## Case Project special configurations

Before deploying the project, confirm your project is properly configured as a Case Project.

1. Open the project `Settings`, and access the options `Deployments`, `General Settings`. 
2. Confirm your project is configured to a `Per case` runtime strategy.
  ![Project  General Settings]({% image_path ccd-project-general-settings.png%}){:width="700px"}

2. Now open the `Marshalling Strategies` menu. Confirm your project has both strategies configured: 

    * `org.jbpm.casemgmt.impl.marshalling.CaseMarshallerFactory.builder().withDoc().get();`
    * `new org.jbpm.document.marshalling.DocumentMarshallingStrategy();`

  ![Project Marshalling Strategies]({% image_path ccd-project-marshalling-strategies.png%}){:width="700px"}

If necessary, adjust your project configurations to match the ones above, and save the project.

## Deploying the project

Now let's try to start a new case. In order to test it we need to deploy it in the execution engine:

1. In Business Central, open your project's _Asset Library_ view.
2. Click on the _Deploy_ button in the upper right corner of the screen. This will package and deploy your project to the Execution Server.
3. The workbench will display 2 green notification bars, stating the build and deployment were successful.

## Starting a new case via REST API 

To confirm the flexibility of the the engine, let's start a new instance of our case via rest API. KIE Server offers a whole list of the available APIs so we can try it out. It is available at the context "/docs". Let's start by opening the. KIE Server Swagger UI.

1. On the Business Central Menu, access the `Execution Servers`. 

2. Next, click on the remote server, and open its URL in a new tab:

   ![KIE Server URL]({% image_path kie-server-url.png%}){:width="600px"}

3. Let's access the swagger ui. On your URL, change the URL context "http://your-url**/docs**". It should look something like: http://insecure-rhpam7-kieserver-rhpam-user1.apps.cluster-e3df.e3df.example.opentlc.com/docs
4. Identify the **Case Instances** section. Under this section, you'll click on the API: 
   * `POST /server/containers/{containerId}/cases/{caseDefId}/instances` *Starts a new case instance for a specified definition*

5. Click on Try it Out, and insert the following data:

   * containerId: `ccd-project`

   * case definition: `ccd-project.ChargeDispute` 

   * Body: 

     ````
     {
       "case-data" : {
         "creditCardholder": {
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

6. You should get a 201 Response, where the Response Body is a Case ID automatically generated, like "CASE-0000000006". 

**TIP:** It will be easier to go through the labs if you have Business Central and KIE Server Swagger UI opened in two different tabs.

Now, to better visualize the progress of this case instance, let's check our case instance on Business Central. 

## Tracking the case instance with Business Central

Business Central offers a monitor view that shows details about each process instance and tasks, and allows also to interact with these items like:

- Starting, aborting, inspecting processes,
- Listing, visualizing, and managing human tasks (claim, start, etc..);
- Check the logs of each step of the process.

Let's use Business Central, which is managing and monitoring the Process Engine (a.k.a Kie Server), to see more details about the case instance you started.

1. Go back to Business Central tab, and open the Menu, "Process Instances". 

   ![Process Instances 1]({% image_path bc-process-instances.png %}){:width="600px"}

    You'll should see one instance listed. If you started more instances via Swagger UI, you'll see more. Click on the case instance listed, and 

    ![Business Central Case Instances Charge Dispute]({% image_path business-central-case-instances-chargedispute.png %}){:width="800px"}

2. Click on the _Diagram_ tab to see the diagram of your case, and the nodes that have been traversed in your case instance. Note that the _Milestone_ is colored grey, what means that it was both triggered (by the _Signal End Event_), as well as completed (by the _Condition_ that evaluated to `true`).

    ![Start Charge Dispute Diagram]({% image_path start-chargedispute-diagram.png %}){:width="800px"}

10. Click on the _Process Variables_ to get an overview of the current state /values of the Case. Note that `fraudData` is indeed set, which was the _Condition_ that completed our _Milestone_.

    ![Start Charge Dispute Case Variables]({% image_path start-chargedispute-case-variables.png %}){:width="800px"}

## Validating your case using Case Management Showcase App

The platform provides a _Case Management Showcase_ application, which is an **example** of how a custom Case Management dashboard could be composed. Let's test your process using it.

1. Open this application by clicking on the _Application Launcher_ button on the top right of the screen. Next, click on _Case Management Showcase_.

    ![Business Central Case Management Showcase Button]({% image_path business-central-cm-showcase-button.png %}){:width="800px"}

2. Login with the same credentials that you used for the Business Central Workbench (u:`pamAdmin`, p:`redhatpam1!`).

3. Locate the blue button `Start Case` and click on it. We will now start a new instance of your Charge Dispute process.

4. A new pop-up should show up. Fill in the `Case Owner` field with value `pamAdmin`, and also the `Users` for the `Role Name` `approval-manager`. Your form should look like this:

  ![Case Management Showcase ChargeDispute List]({% image_path showcase-app-new-case-popup.png %}){:width="800px"}

5. Click on `Start`. The showcase application will list the opened case. You will see the case you've just started in the list:

    ![Case Management Showcase ChargeDispute List]({% image_path cms-chargedispute-case-list.png %}){:width="800px"}

4. Click on the case to open a detailed view of the open case. This view will, among other things, list the completed milestones, the available actions, the actions that are in progress, the completed actions and the case roles. Take some time to explore the details of your case.

    ![Case Management Showcase ChargeDispute Details]({% image_path cms-chargedispute-case-details.png %}){:width="800px"}

# Conclusion

So far we've configured the initial process, a `signal end-event` that triggers the `milestone`, and a `milestone` conditional expression which fired based on the `case data`.

In the next section we will add our rules, decisions and user-tasks to our project.
