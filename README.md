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
   
![](data:image/png;base64,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)

3. From the left hand menu, go to **Project Settings**![]
4. From the available tiles select “Discovery”
5. After expanding the side menu of the “DA-K8s” agent click on “Discover Now”


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
2. From the left hand side menu select pipelines and drill down to the existing pipeline
3. In the existing pipeline, within the Deploy backend stage **after** Canary Deployment and **before** the approval step click on the plus icon to add a new step

4. Add a **Verify** step with the following configuration

| Input                        | Value  | Notes                                                                                            |
| ---------------------------- | ------ | ------------------------------------------------------------------------------------------------ |
| Name                         |Verify|                                                                                                  |
| Continuous Verification Type |Canary|                                                                                                  |
| Sensitivity                  |Low| This is to define how sensitive the ML algorithms are going to be on deviation from the baseline |
| Duration                     |10mins|                                                                                                  |

5. Under the verify step click on the plus icon to add a new step in parallel
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
