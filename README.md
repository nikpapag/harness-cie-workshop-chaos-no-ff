# Lab 1 - Build

### Summary: Setup a CI Pipeline, including running source code tests, building the executable, building and pushing the artifact to a remote repository

### Outcome: A Deployable artifact

### Learning Objective(s):

- Configure a basic pipeline using Harness CIE

- Build and Deploy an artifact to a remote repository using Harness CIE

- Run unit tests during the process to verify that the build is successful using Harness CIE

**Steps**

1. From the left hand menu, navigate to **Projects** → **Select the project available**\
   ![](https://lh7-us.googleusercontent.com/docsz/AD_4nXfhuMykMsIHl-7FjliWssHc0uwRpdLdrnq7GkGAI0g6UBZM69F1zpQ8ZA8N_vMqjpoGFYFR_weJk7OtOGGa2bksIaS6BlktwytmuJ1THM3e8O6tDT18HYWwFyGUye8ubsrHBChI8ORrCQ88JcKWpLjQ0DsXDS0NSZrkfZ4RUQ?key=cRG2cvp_PHVW0KG2Gq6Y_A)

1) From the left hand side menu select **Pipelines**

2) Click **+ Create a Pipeline**, enter the following values, then click **Start**

| Field                                  | Value            | Notes
| -------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------ |
| Name                                   |workshop|                                                                                            |
| How do you want to setup your pipeline |Inline| This indicates that Harness (rather than Git) will be the source of truth for the pipeline |

3. From Pipeline Studio, Click **Add Stage** and select **Build** as the Stage Type

4. Enter the following values and click on **Set Up Stage**



| Input           | Value           | Notes |
| --------------- | --------------- | ----- |
| Stage Name      |Build|       |
| Clone Codebase  |Enabled|       |
| Repository Name |harnessrepo|       |

5. There are two main tabs that need configuration:\
   **Infrastructure**



| Input          | Value | Notes |
| -------------- | ----- | ----- |
| Infrastructure |Cloud|       |

**Execution**

- Select **Add Step**, then **Add Step** again, then select **Run Tests** from the Step Library and configure with the following



| Input                        | Value                                      | Notes                                                                                                                                             |
| ---------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                         |Run Tests With Intelligence|                                                                                                                                                   |
| Language                     |Python|                                                                                                                                                   |
| Build Tool                   |Pytest|                                                                                                                                                   |
| Build Arguments              | Leave empty                                | Harness recognises the framework “pytest” and will fill in the gaps at runtime                                                                    |
| Test Report Paths            | Leave empty                                | Harness automatically parses the current directory to identify test results                                                                       |
| **Additional Configuration** |                                            |                                                                                                                                                   |
| Pre-Command                  |pip install pytest & cd ./python-tests| The github repo is a monorepo with application(s) and configuration in the same repo. Therefore we need to navigate to the application subfolder. |
| Run only selected tests      |Enabled| Activate Test Intelligence                                                                                                                        |

- After completing configuration select **Apply Changes** from the top right of the configuration popup

- Select **Add Step**, then **Use template** (In this step we will be building the binary following same config as before. To avoid duplication of efforts a template has been precreated)



| Input         | Value               | Notes |
| ------------- | ------------------- | ----- |
| Template Name |Compile Application|       |

- Select the  template and press **Use Template,** then provide a name for that template



| Input | Value   | Notes |
| ----- | ------- | ----- |
| Name  |Compile|       |

- Select **Add Step**, then **Add Step** again, then select **Build and Push an image to Docker Registry** from the Step Library and configure with the following



| Input             | Value                                               | Notes                                                                    |
| ----------------- | --------------------------------------------------- | ------------------------------------------------------------------------ |
| Name              |Push to DockerHub|                                                                          |
| Docker Connector  |dockerhub|                                                                          |
| Docker Repository |nikpap/harness-workshop|                                                                          |
| Tags              |<+variable.username>-<+pipeline.sequenceId>| This will be the tag of the image using harness expressions              |
| Dockerfile        |/harness/frontend-app/harness-webapp/Dockerfile| This tells harness where is the Dockerfile for building the app          |
| Context           |/harness/frontend-app/harness-webapp| This tells from where to run the instructions included in the dockerfile |

1. Click **Apply Changes** to close the config dialog

6) Click **Save** and then click **Run** to execute the pipeline with the following inputs


| Input       | Value | Notes        |
| ----------- | ----- | ------------ |
| Branch Name |main| prepopulated |


# Lab 2 - DevSecOps

**Summary:** Our security team has implemented orchestration of **Fortify** and **OWASP** scans for our code in a reusable form **(templates)**. In order to improve our security posture they have also added policies to enforce us to include those templates

![](https://lh7-us.googleusercontent.com/docsz/AD_4nXcLr5TGcKRWOjVgB_sCAHHEeLPyd6EBdnkt2-mq_imTkZbQMEwJD03Q1wZyhWqHxoCNIIYWJWlRbnZrvZn2pPYIwTzXlOGdhMDEgn-J2JnK7lVastmfpdwTqDHXjpP0DK3TgU1gM-Ec_0iZLicWV7KpgW2FdXUCcAtraDGaEz8hI3dpWGLXkg?key=cRG2cvp_PHVW0KG2Gq6Y_A)

**Learning Objective(s):**

- Understand how governance plays a role in the path to production

- Reusable templates make developer’s life easier

- DevSecOps practices can be easily achieved

**Steps**

1. In the existing pipeline, within the Build stage **before** PushToDockerhub step click on the plus icon to add a new step

2. Select use template\
   ![](https://lh7-us.googleusercontent.com/docsz/AD_4nXeC5rTVxlk7DeZeU_cINwcKo6Nf2wVW9brQ9MiCEfppJwmU-uH3QcNZ53qTxhur57KeySksoDBg9EqjhgKOgAEDKon6iNz9cFxozBe9VZssV-t77VNo6t1zPUvm6e2NOZJDKncxd9c2GM4HE-h-L4cIOl4u6Uqx_azoKchMdg?key=cRG2cvp_PHVW0KG2Gq6Y_A)

3. Select **DevX Fortify Scan** 

4. Name the step **Fortify**

5. In the existing pipeline, within the Build stage **after** PushToDockerhub step click on the plus icon to add a new step

6. Select use template

7. Select **OWASP**

8. Name the step OWASP

9. Click **Save** and then click **Run** to execute the pipeline with the following inputs



| Input       | Value | Notes |
| ----------- | ----- | ----- |
| Branch Name |main|       |

After the **Build and Push** stage is complete, go to the **Security Tests** tab to see the deduplicated, normalized and prioritized list of vulnerabilities discovered across your scanners.


# Lab 3 - Continuous Deploy - Frontend

### Summary: Extend your existing pipeline to take the artifact built in the CI/Build stage and deploy it to an environment

**Learning Objective(s):**

- Add a second stage to an existing pipeline

- Create a k8s service

- Incorporate an advanced deployment strategy such as Canary

- Create custom Harness variables

- Create an Input Set

**Steps**

2. In the existing pipeline, add a Deployment stage by clicking **Add Stage** and select **Deploy** as the Stage Type

3. Enter the following values and click on **Set Up Stage**


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Stage Name      |frontend|       |
| Deployment Type |Kubernetes|       |

4. Configure the **frontend** Stage with the following\
   **Service**

- Click **+Add Service** and configure as follows****


| Input                      | Value                                               | Notes                              |
| -------------------------- | --------------------------------------------------- | ---------------------------------- |
| Name                       |frontend|                                    |
| Deployment Type            |Kubernetes|                                    |
| * **Add Manifest**         |                                                     |                                    |
| Manifest Type              |K8s Manifest|                                    |
| K8s Manifest Store         |Code|                                    |
| Manifest Identifier        |templates|                                    |
| Repository                 |harnessrepo|                                    |
| Branch                     |main|                                    |
| File/Folder Path           |harness-deploy/frontend/manifests|                                    |
| Values.yaml                |harness-deploy/frontend/values.yaml|                                    |
| - **Add Artifact Source**  |                                                     |                                    |
| Artifact Repository Type   |Docker Registry|                                    |
| Docker Registry Connector  |dockerhub|                                    |
| Artifact Source Identifier |frontend|                                    |
| Image Path                 |nikpap/harness-workshop|                                    |
| Tag                        |<+variable.username>-<+pipeline.sequenceId>| _Select value, then click on the pin and select expression and paste the value |

**Environment**

The target infrastructure has been pre-created for us. The application will be deployed to a k8s cluster on the given namespace  

- Click **- Select -** on the environment input box ****

- Select **prod** environment****

| Input | Value | Notes                                                             |
| ----- | ----- | ----------------------------------------------------------------- |
| Name  |prod| Make sure to select the environment and infrastructure definition |

- Click **- Select -** on the environment input box ****

-  From the dropdown select k8s



| Input | Value | Notes |
| ----- | ----- | ----- |
| Name  |k8s|       |

**Execution**

- Select **Rolling** and click on **Use Strategy**, the frontend is a static application so no need to do canary, new features will be managed by Feature Flags at a later stage of this lab


# Lab 4 - Continuous Deploy - Backend

### Summary: Extend your existing pipeline to derisk production deployments

**Learning Objective(s):**

- Utilise complex deployment strategies to reduce blast radius of a release 

**Steps**

5. In the existing pipeline, add a Deployment stage by clicking **Add Stage** and select **Deploy** as the Stage Type

6. Enter the following values and click on **Set Up Stage**


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Stage Name      |backend|       |
| Deployment Type |Kubernetes|       |

7. Configure the **backend** Stage with the following\
   **Service**

- Click **Select Service** and configure as follows****


| Input | Value       | Notes |
| ----- | ----------- | ----- |
| Name  |backend|       |

**Environment**

The target infrastructure has been pre-created for us and we used it in the previous stage. To reuse the same environment

- Click **- Propagate Environment From**

- Select **Stage \[frontend]**

**Execution**

- Select **Canary**  and click on **Use Strategy**

- **After** the canary deployment and **before** the canary delete step add **Harness Approval** step according to the table  below

| Input            | Value            | Notes |
| ---------------- | ---------------- | ----- |
| Step Name        |Approval|       |
| Type of Approval |Harness Approval|       |

- Configure the Approval step as follows

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |Approval|       |
| User Groups |All Project Users|       |


8. Click **Save** and then click **Run** to execute the pipeline with the following inputs. As a bonus, save your inputs as an Input Set before executing (see below)

| Input       | Value | Notes       |
| ----------- | ----- | ----------- |
| Branch Name |main| Leave as is |

9. While the canary deployment is ongoing and waiting **approval** navigate to the web page and see if you can spot the canary (use the check release button) 

| project                | domain        | suffix |
| ---------------------- | ------------- | ------ |
|http\://project_id|.cie-bootcamp|.co.uk|

![](https://lh7-us.googleusercontent.com/docsz/AD_4nXfmb1N3lAe0EOnEun9neU9y3ilqy3HbxfnWfUMzF3FsykslwgQfU_W4pE0wlt5kYSp6_mTs7cVP0anhJ7uvtsytal2qX3ZEq3vvOT3DOBUzE9SZ3rpwkAHP6e_ExdRbo5VmN2kpxdFlp6u8iGaKwhW_uyAohEmJurkjmEB2Ww?key=cRG2cvp_PHVW0KG2Gq6Y_A)

10. Approve the canary deployment for the pipeline to complete

# Lab 6 - Chaos Engineering


### Summary: Fully integrated chaos experiments with the delivery process

**Learning Objective(s):**

- Auto generate chaos experiments on deployed services
- Build a chaos experiments using a base fault (out of 200 OOTB faults)
- Embed chaos engineering experiments into the deployment process
- Add continuous verification to the deployed service
- Automate release validation

**Steps**

1. From the module selection menu select chaos engineering

   ![Screenshot 2024-11-28 at 14 07 39](https://github.com/user-attachments/assets/5c520265-658f-4953-a95c-7a5c3c57ecdf)

4. From the left hand menu, go to **Project Settings**![]
5. From the available tiles select “Discovery”
6. After expanding the side menu of the “DA-K8s” agent click on “Discover Now”
   ![Screenshot 2024-11-28 at 14 23 49](https://github.com/user-attachments/assets/05671080-0e6d-4d93-9298-16aa7f7e7824)


**Create Application Map**

1. Wait for the discovery agent to complete (usually 1-2 minutes)
2. After discovery is complete double click on the agent “DA-K8s”
3. Select the "Application Maps" tab
4. Click on **Create New Application Map** and enter the following values

| Input                        | Value|  
| ---------------------------- | ------ |
| Name                         |workshop-am|

5. Select the relevant services for your project name "use the search function to find the services"
6. Click Save

**Create Experiments Automatically**

1. From the left hand menu, go to **Application Maps**
2. Select the previously created application map (It is normal for the page to be loading while calculating the resiliency score)
3. From the navigation menu select **Chaos Experiments**
4. From the available list of fault profiles select **Only a few**
5. Double click on the web-frontend experiment and run it
    1. Observe the logs and validate the application’s resiliency by navigating to the app

**Create Experiments manually**

1. From the left hand menu, go to **Chaos Experiments**
2. Select **+New Experiment**

| Input                        | Value|  
| ---------------------------- | ------ |
| Name                         |pod-memory|

3. Select **Harness Infra**
   ![Screenshot 2024-11-28 at 14 24 21](https://github.com/user-attachments/assets/c47834a3-fe88-44ed-be7e-7cee97bcb303)

4. On the popup window select the available options

| Input                        | Value|  
| ---------------------------- | ------ |
| Select Environment|prod|
| Select Infrastructure|k8s|

5. Click on next to navigate to the experiment builder
6. Start with blank canvas
7. Click on **Add**
8. From the list of available faults select **Pod Memory Hog**
9. Target Application Inputs (Warning: Read the notes on the table below)

| Input       | Value | Notes       |
| ----------- | ----- | ----------- |
| Select App Kind |deployment| Leave as is |
| Namespace |**_&lt;org_name&gt;-ns_** | Org_name is a placeholder replace with your organisation |
| Name |backend-&lt;project_name&gt;-deployment-canary| &lt;project_name&gt; is a placeholder replace with your project |

10. Tune Fault

| Input       | Value |
| ----------- | ----- |
| Total Chaos Duration |300|
| Memory Consumption |300|
| Number of workers |1|
| Pod affected percentage|100|

11. For the probes click on +Select or Add new probes
12. Select the default probe and click on **Add to Fault**
13. For the probe mode select **Continuous** and click on **Apply Changes**
14. Save the experiment

**Embed chaos experiments into CD pipelines**

1. From the module selection menu select Continuous Delivery & GitOps
   ![Screenshot 2024-11-28 at 14 07 22](https://github.com/user-attachments/assets/898ee27b-7369-47c6-a145-e74b49bb4bed)
   
3. From the left hand side menu select pipelines and drill down to the existing pipeline

4. In the existing pipeline, within the Deploy backend stage **after** Canary Deployment and **before** the approval step click on the plus icon to add a new step

5. Add a **Verify** step with the following configuration

| Input                        | Value  | Notes                                                                                            |
| ---------------------------- | ------ | ------------------------------------------------------------------------------------------------ |
| Name                         |Verify|                                                                                                  |
| Continuous Verification Type |Canary|                                                                                                  |
| Sensitivity                  |Low| This is to define how sensitive the ML algorithms are going to be on deviation from the baseline |
| Duration                     |10mins|                                                                                                  |

5. Under the verify step click on the plus icon to add a new step in parallel
   ![Screenshot 2024-11-28 at 14 28 38](https://github.com/user-attachments/assets/368ba808-d303-43f8-8824-5d2e09367b01)

6. Add a **chaos** step with the following configuration

| Input                        | Value  |
| ---------------------------- | ------ |
| Name                         |Chaos|
| Select Chaos Experiment |pod-memory|
|  Expected Resilience Score|50| 

7. Click on Apply Changes

8. Click **Save** and then click **Run** to execute the pipeline with the following inputs. As a bonus, save your inputs as an Input Set before executing (see below)

| Input       | Value | Notes       |
| ----------- | ----- | ----------- |
| Branch Name |main| Leave as is |


# Lab 7 - Validate Release

**Learning Objective(s):**

- Identify the difference in traffic between normal and canary instances of the application

- Automate release validation

- Use complex deployment strategies to reduce the blast radius

**Steps**:

- While the canary deployment is ongoing navigate to the web page and see if you can spot the canary (use the check release button) 

| project                | domain        | suffix |
| ---------------------- | ------------- | ------ |
| http\://\<project\_id>|.cie-bootcamp|.co.uk|

- Drill down to the distribution test tab and run the traffic generation by clicking the **Play** button

- Observe the traffic distribution

- Validate the outcome of the verification on the pipeline execution details

\
![](https://lh7-us.googleusercontent.com/docsz/AD_4nXdbAmEJ5zQPsKlw_nEknWvYo97pm5eWCXr6vU8-GgIL0ulAOSH9N07PoEcVSknARVQo7Tgj1s31VHqR1I3hu2dMIO1rIX5HHcmTPXoQPoyo8CPv13OhnJN5WVcZqSwUXzdDHmm3PxUnhtpGVl0PAMJ_1wnuodvUbVPBOdnGKQ?key=cRG2cvp_PHVW0KG2Gq6Y_A)
![](https://lh7-us.googleusercontent.com/docsz/AD_4nXf-5oWX9OfvdmEb9MBm2_h2KKAa_QwmiJoM0fiKrTuxAr6GR4wxeulSlk48gyBK3dykrtIslDSkxpiGytrxH0JaxaQ4ZgTYxbmc8OenAH3nhGCvvOAxkWVjVBp1TRg_qQQi9z8OrNPK4udPtNL1LIyym6Ch5IMzrulFOcXhOQ?key=cRG2cvp_PHVW0KG2Gq6Y_A)

**Bonus**:

- Add a canary rollout from 10% to 50% traffic and see how this impacts the traffic distribution


# Lab 8 - Governance/Policy as Code

### Summary: Create and apply policies as code in order to enable governance and promote self-service. In Lab 2 we saw how a user is impacted by policies in place, now is the time to create such policies

**Learning Objective(s):**

- Create a policy that evaluates when editing pipelines

- Create a policy that evaluates during pipeline execution

- Test policy enforcement

**Steps\
****Create a Policy to require Approvals**

1. From the secondary menu, select **Project Settings** and select **Governance Policies**

2. Click **Build a Sample Policy**

3. From the suggested list select **Pipeline - Approval**  and click on next

4. Click Next: Enforce Policy

5. Set the values according to the table  below and confirm

| Input            | Value        | Notes |
| ---------------- | ------------ | ----- |
| Trigger Event    |On Run|       |
| Failure Strategy |Error & exit|       |

**Test the Policy to require Approvals**

1. Open your pipeline

2. Try to run the pipeline and note that the failure due to lack of an approval stage

3. Click **Save** and note that the failure due to lack of an approval stage

4. Open the pipeline in edit mode and navigate to the “**frontend**” stage

5. Before the rolling deployment step add **Harness Approval** step according to the table  below

| Input            | Value            | Notes |
| ---------------- | ---------------- | ----- |
| Step Name        |Approval|       |
| Type of Approval |Harness Approval|       |

6. Configure the Approval step as follows

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |Approval|       |
| User Groups |All Project Users|       |

7. In a similar way as before navigate to the “**backend**” stage
8. Before the canary deployment block add **Harness Approval**
10. Click **Save** and note that the save succeeds without any policy failure


# Lab 9 - Governance/Policy as Code (Advanced)

**Create a Policy to block critical CVEs**

1. From the secondary menu, select **Project Settings** and select **Policies**

2. Select the **Policies** tab 

3. click **+ New Policy**, set the name to **Runtime OWASP CVEs** and click **Apply**

4. Set the rego to the following and click **Save**

<!---->

    package pipeline_environment
    deny[sprintf("Node OSS Can't contain any critical vulnerability '%d'", [input.NODE_OSS_CRITICAL_COUNT])] {  
       input.NODE_OSS_CRITICAL_COUNT != 0
    }

5. Select the **Policy Sets** tab

6. Click **+ New Policy Set** and configure as follows

| Input                      | Value                     | Notes |
| -------------------------- | ------------------------- | ----- |
| Name                       |Criticals Not Allowed|       |
| Entity Type                |Custom|       |
| Event Evaluation           |On Step|       |
| Policy Evaluation Criteria |                           |       |
| Policy to Evaluate         |Runtime OWASP CVEs|       |

7. For the new policy set, toggle the **Enforced** button

**Add Policy to Pipeline**

1. Open your pipeline

2. Go to an execution that already ran, and copy the CRITICAL output variable from the OWASP step like so:\
   ![](https://lh7-us.googleusercontent.com/docsz/AD_4nXfYQ7ba5Q_cQ9xy2AFVZ5Mt0iZPYbyQDmBonp0pBQA13Z_IUeYdK8gRSbddtf_V3bSRfbhKWDbRSUVJTx3BTCc_VmwLIWyWLkdh89nLh0sEBA6fqQxTy0NADZ0YPZwCirNycRVGUQACdItaBotovPs5Hg6CmRpQHk5ysgV6RUlhSbIbkNxmHAo?key=cRG2cvp_PHVW0KG2Gq6Y_A)

3. Select the **frontend** stage

4. Before the **Rollout Deployment** Step Group, add a **Policy** type step and configure as follow

| Input       | Value                                          | Notes                                                                                                                                                   |
| ----------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name        |Policy - No Critical CVEs|                                                                                                                                                         |
| Entity Type |Custom|                                                                                                                                                         |
| Policy Set  |Criticals Now Allowed| Make sure to select the Project tab in order to see your Policy Set                                                                                     |
| Payload     |{"NODE\_OSS\_CRITICAL\_COUNT": _\<variable>_}| Set the field type to Expression, then replace _\<variable>_ with OWASP output variable CRITICAL. Go to a previous execution to copy the variable path. |

5. Save the pipeline and execute. Note that the pipeline fails at the policy evaluation step due to critical vulnerabilities being found by OWASP.






[image1]: <data:image/png;base64,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>
