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
2. From the left hand menu, go to **Project Settings**![](data:image/png;base64,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0Ucfme9974dm5sxZblOdaMGChbL+bdu2u01UDatBAPmZF1yTBA40TdesEWdu7KV3dIF84sRJchG5556epm/f+wOvWLHCnbTK6tOnrznvvPPd0bUmXPCxL4ASe1/gkydPmgcffKhOt4dquMolIF+wYIH83eNvu2fPe02zZr+T4a5du5lYLGb+9a/PZfiFF5a6s9ap0sXngQMH3MmrLGSnBwx42Lz77t/dpjrRzp07Zf0HDx50m6gaVoMA8h+c+6skeKBpumaNOHNjL72jD+RHjhxxmwLt27dPMunIwp06dSqhDRfBV19dZ5YseUYyQ9g2CL+xXPill14SKCgsLJLfe/fuC+bftWuXN+5l+f3ll19KOy5oGLdly5ZgunTboNIL/mOPDXObRGFA/v7775tnn33OrF692hw+fDihDevZvHmzgAwyeFu3bjVvvvlm0P7NN9+Yl19+xaxdu5YX4YgpV4B8x4535G++TZu28vevGjduvIxHVtwGckDj88+/YLZv32EtxVe6WMBxQrsuA6BvK5tYyBSfen74/PPPJduN7XzvvffdyeRcgO1AbOK8hXm+/fbbpPML9h3bjOUtX75Cpscxt4UYx3kGy/vkk08S2iAcB+wXjgmeNKjCzlU67sSJE9XeFyq9GgSQ46J/xs+bJgEETdM1Y8QX4syNvfTOXSAfM2ZsANbwpZdeJhdfCDB91llny3j9F4/Nod697wvmQZtCOYYB76rhw0fIOAgXTfy++uprE5aVbhtsZbrgu0CObJa97fj3008/lTbcaCCTZ+8D1nvTTbdIO+Bc58My8e+GDRuDZVMNW7kC5NOmTZe/PRsWITwRQmy/8cYbQdzdemuLhDiaMmVqMH26WAC0tmhxR8K87dq1D24Aso2FTPGp24kYs9eFJwCqZ555NqHt97//g/xr76eeXy688OLgaYEaTw1UmF63Vz1r1uNB+8aNf05owz5qkiDsXKXjcNyquy9UejUIIIdx4UcGj+UrNF1zRjwhrqoO43D0gbxfv/5myJChgQHoyNqgbd68+bK+zz77TC5KAFto8eIlpmXLVpIFQjtKVDA9ascht2TFvWBCYUCen99D1o9lZtoGW3rBx0XS3peVK1dKuw3keETeoUNHqTfV5WLesWPHSfujjw6R4XXr1styFy9eLMMK5L169ZZhgDuyhYCMCRMm+htCNXjlCpB3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)
3. From the available tiles select “Discovery”
4. After expanding the side menu of the “DA-K8s” agent click on “Discover Now”





1. In the existing pipeline, within the Deploy backend stage **after** Canary Deployment and **before** the approval step click on the plus icon to add a new step

2. Add a **Verify** step with the following configuration

| Input                        | Value  | Notes                                                                                            |
| ---------------------------- | ------ | ------------------------------------------------------------------------------------------------ |
| Name                         |Verify|                                                                                                  |
| Continuous Verification Type |Canary|                                                                                                  |
| Sensitivity                  |Low| This is to define how sensitive the ML algorithms are going to be on deviation from the baseline |
| Duration                     |5mins|                                                                                                  |

Click **Save** and then click **Run** to execute the pipeline with the following inputs. As a bonus, save your inputs as an Input Set before executing (see below)

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
