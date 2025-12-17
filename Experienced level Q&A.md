*** 4 to 6 years of devops engineer Q&A ***

#can you explain me like what are the various stages in a CI CD pipeline?
```
So, based on the requirement, we can able to create the stages. In my present organization, what I worked with the project, right? In that project level, we have nearly 13 to 14 stages. 
We are using the declarative pipeline, so we don't want to create any kind of checkout related stages.That will create automatically declarative checkout stage.

The next stage will be the Jira ID validation. When we raise a pull request, what kind of bug or future enhancement is trying to merge into the specific environment.
These kind of things, collection purpose, which we enable the Jira ID validator.

The next stage would be the unit testing. Using Maven tool, which we are running the unit test cases, it is required for the sonar quality gates getting purpose.

The next stage would be the Shast analysis, sonar cube and checkbox related scans, which we integrated over there.

The next stage would be the actual building the artifact. Using Maven tool, JAR or VAR content generation will be happen.

The next stage would be the dockerization.Here we are using the docker file and generated artifact using those two files, which we are generating the docker image.

The next stage would be the docker image scan. Using Twistlock or Trivy is a private security tool, which is used to do the scans.
And another would be the JFrog X-ray, which we are using to get the vulnerability. If any CEV-1, CEV-2 related vulnerabilities, which identified by those security tools, the build will be fail there
itself. Otherwise, it will move to the next stage.

The next stage level re-tagging of our image and uploading that into the JFrog artifactory docker registry level to maintain our future reference purpose.

The next stage would be the environment preparation. Here we have a Kubernetes manifest file in the root directory.We were utilizing three stages here.
 Based on the when condition, if the branch pattern matches, the specific stage will be executed. Remaining two stages will be skipped. Like that, we have defined the pipeline related stack.
 After environment preparation is completed,

the next stage would be the deployment phase. Here also we have three stages.
The stage will be executed based on the when condition itself. If the branch pattern is matched,that specific stage will be executed to inject that latest changes to the Kubernetes cluster level
handling purpose. Once the deployment is succeeded.

the next stage level, we were running the downstream job.
End-to-end job we were triggering. That end-to-end job, if more than 85% of the test cases are passed, the build will be succeeded.
Otherwise, the downstream job will be failed and the particular stage will be failed.
After stage is failed, automatically it will move to the next stage. If it is passed, it will move to
the post-build actions.

 The next stage, the failure cases, the next stage will be the rollback will
happen.

Once the rollback is succeeded, the next post-build actions level, it will do the cleanup of the
always section, clean up the workspace to reduce the space related issues. And failure related
email triggers and successful related email triggers which we are enabled.

```
