To set up a **Multi-branch Pipeline with Jenkins**, follow these steps to automatically detect and build different branches (e.g., `development`, `staging`, `production`) with branch-specific tasks. This is a powerful setup for managing different environments and testing processes for each branch.

---

### Step-by-Step Implementation

#### 1. **Prepare the Project Repository with Branches**

   - Create a Git repository (GitHub, GitLab, or Bitbucket).
   - Set up a simple Java or any other application in the repository.
   - Create branches named `development`, `staging`, and `production`.

   Here’s an example of how you might structure these branches:
   ```bash
   # Create and push a 'development' branch
   git checkout -b development
   git push origin development

   # Create and push a 'staging' branch
   git checkout -b staging
   git push origin staging

   # Create and push a 'production' branch
   git checkout -b production
   git push origin production
   ```

#### 2. **Install Jenkins and Required Tools on Linux**

   - Install Jenkins on your Linux server if not already done.
   - Make sure Git and Maven are installed on the Jenkins server so Jenkins can use them for building projects.
   
   ```bash
   sudo apt update
   sudo apt install git maven
   ```

#### 3. **Create a Jenkins Multi-branch Pipeline Project**

   - Open Jenkins and go to **New Item**.
   - Enter a name for your job (e.g., `MyApp-MultiBranch`).
   - Select **Multibranch Pipeline** and click **OK**.

#### 4. **Configure Source Code Management in Jenkins**

   - In the job configuration, go to the **Branch Sources** section.
   - Select **Git** and add the URL of your repository.
   - If your repository is private, you may need to add credentials.

   Jenkins will automatically scan the repository for branches, and it will create separate jobs for each branch it detects.

#### 5. **Define a Branch-specific Jenkinsfile**

   - In each branch of your Git repository, create a `Jenkinsfile` to define branch-specific build steps.
   - Here’s an example `Jenkinsfile` that performs different tasks based on the branch.

   ```groovy
   pipeline {
       agent any
       tools {
           maven 'Maven'  // Specify Maven version configured in Jenkins
       }
       stages {
           stage('Build') {
               steps {
                   script {
                       if (env.BRANCH_NAME == 'development') {
                           echo 'Building development environment...'
                           sh 'mvn clean install -Denvironment=dev'
                       } else if (env.BRANCH_NAME == 'staging') {
                           echo 'Building staging environment...'
                           sh 'mvn clean install -Denvironment=staging'
                       } else if (env.BRANCH_NAME == 'production') {
                           echo 'Building production environment...'
                           sh 'mvn clean install -Denvironment=prod'
                       }
                   }
               }
           }
           stage('Test') {
               steps {
                   sh 'mvn test'
               }
           }
           stage('Deploy') {
               steps {
                   script {
                       if (env.BRANCH_NAME == 'production') {
                           echo 'Deploying to production server...'
                           // Add production deployment steps here
                       } else if (env.BRANCH_NAME == 'staging') {
                           echo 'Deploying to staging server...'
                           // Add staging deployment steps here
                       } else {
                           echo 'Development build complete. No deployment for development branch.'
                       }
                   }
               }
           }
       }
       post {
           success {
               echo "Build for ${env.BRANCH_NAME} branch was successful!"
           }
           failure {
               echo "Build for ${env.BRANCH_NAME} branch failed."
           }
       }
   }
   ```

#### 6. **Automate Scans for New Branches**

   - In the **Multi-branch Pipeline** configuration, scroll down to **Scan Multibranch Pipeline Triggers**.
   - Enable the **Periodic Scan** option and set an interval (e.g., every 1 hour) to automatically detect new branches in the Git repository.
   - Jenkins will scan the repository periodically and create jobs for any new branches it finds.

#### 7. **Run the Multi-branch Pipeline**

   - Once configured, Jenkins will create individual pipeline jobs for each branch (`development`, `staging`, and `production`).
   - When code is pushed to any branch, Jenkins will trigger the pipeline for that specific branch.
   - Each branch will follow the tasks defined in the `Jenkinsfile`, including branch-specific build and deployment steps.

#### 8. **Branch-based Deployment Strategy**

   - **Development branch**: Run build and basic tests without deployment.
   - **Staging branch**: Run build, tests, and deploy to a staging environment.
   - **Production branch**: Run build, tests, and deploy to a production environment with extra verification if needed.

#### 9. **View Branch Builds in Jenkins**

   - Go to the **Multi-branch Pipeline** project dashboard.
   - You’ll see separate builds for each branch, allowing you to monitor the build history and status for each environment.

---

### Learning Goals Recap

- **Jenkins Multi-branch Pipeline**: Automates builds for different branches with a single configuration.
- **Git Branch Management**: Manage `development`, `staging`, and `production` workflows within the same project.
- **Branch-based Deployment Strategy**: Customize tasks and deployments per branch, allowing you to control and automate environment-specific processes.

This setup is powerful for real-world CI/CD workflows and ensures that each environment is treated with the appropriate care, reducing errors and increasing efficiency. Let me know if you’d like to dive deeper into any specific part of this setup!
