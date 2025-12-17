# Jenkins-CI-CD-pipeline


CI/CD PIPELINE (Jenkins Declarative)
```
│
├── 1. Source Code Trigger
│     └── Git Push / Pull Request (GitHub / GitLab)
│
├── 2. Declarative Checkout (Auto)
│     └── Jenkins declarative pipeline auto-checkout
│
├── 3. Jira ID Validation
│     └── Validate Jira ticket (Bug / Story / Feature)
│     └── Block merge if Jira ID is invalid
│
├── 4. Unit Testing
│     └── Maven test
│     └── Required for Sonar Quality Gates
│
├── 5. Static Code Analysis (SAST)
│     ├── SonarQube Scan
│     └── Code Quality + Coverage Check
│
├── 6. Build Artifact
│     └── Maven package (JAR / WAR)
│
├── 7. Docker Image Build
│     └── Dockerfile + Artifact
│     └── Docker image creation
│
├── 8. Docker Image Security Scan
│     ├── Twistlock Scan
│     └── JFrog X-Ray Scan
│         ├── CVE-1 / CVE-2 Found → ❌ FAIL BUILD
│         └── No Critical Issues → Continue
│
├── 9. Image Re-Tagging & Push
│     └── Retag image (version / build number)
│     └── Push to JFrog Artifactory (Docker Registry)
│
├── 10. Environment Preparation (Kubernetes)
│     ├── Dev Env (when: dev branch)
│     ├── QA Env (when: qa branch)
│     └── Prod Env (when: main/release branch)
│
├── 11. Deployment Stage (Kubernetes)
│     ├── Dev Deployment
│     ├── QA Deployment
│     └── Prod Deployment
│     └── kubectl apply / rollout
│
├── 12. Downstream Job Trigger
│     └── End-to-End Automation Job
│     └── Test Pass Criteria ≥ 85%
│
├── 13. Rollback (Failure Handling)
│     └── Auto rollback to previous stable version
│
└── 14. Post-Build Actions
      ├── Workspace Cleanup (always)
      ├── Success Email Notification
      └── Failure Email Notification

```
![Jenkins CI_CD Kubernetes-2025-12-17-070944](https://github.com/user-attachments/assets/1575faf0-7e22-4f82-b275-3c6199b171b7)






