Open jenkins main page.

Open the pipeline's folder location. If there isn't any, create one by
- Clicking "New Item" in the top left sidebar
- Select "Folder"
- Enter the name in the "Enter an item name" textbox
- Click "OK"

Inside the desired folder, click "New item" to create a pipeline. There are several types of projects one can choose:

1. **Freestyle Project**
   - Basic, all-purpose project
   - It's the simplest and most flexible type.
   - Build, test, and deploy code using simple GUI configuration.
   - Good for small projects or when you're just getting started with Jenkins.
   - Can run shell/batch scripts, call build tools, and send artifacts somewhere.
   - **Use it when**: You just want to automate a few steps and don’t need complex branching or scripting.

2. **Maven Project**
   - Designed specifically for Maven-based Java projects.
   - Jenkins understands the structure of Maven projects.
   - Automatically detects and runs `pom.xml` build lifecycle phases.
   - Offers built-in support for running tests and publishing results.
   - **Use it when**: You’re working with a Java project that uses Maven as its build tool.   

3. **Pipeline**
   - Scripted, powerful, and flexible automation defined in code (Jenkinsfile).
   - Uses Groovy-based scripts to define complex build workflows.
   - Can handle stages like build → test → deploy.
   - Ideal for modern CI/CD processes.
   - Easily version-controlled by storing the `Jenkinsfile` in your code repository.
   - **Use it when**: You want full control and repeatability for your build/deploy process, or need multi-step workflows.

4. **External Job**
   - Tracks a job that Jenkins doesn’t run itself.
   - Used to monitor builds that are triggered outside Jenkins.
   - Jenkins just listens to the result/output via logs.
   - Very rarely used today.
   - **Use it when**: You want Jenkins to **monitor** an external process (like a build script started by cron or another system).

5. **Multi-configuration project**
   - Run the same job in many different environments or configurations.
   - Great for testing across multiple platforms (e.g., Windows/Linux, different JDKs).
   - Automatically creates a matrix of combinations and runs jobs in parallel.
   - **Use it when**: You need to test code in multiple setups, like various OSes, browsers, or Java versions.

6. **Multibranch Pipeline**
   - Automatically creates pipelines for each branch of your project.
   - Scans your Git repository for branches and creates a separate pipeline for each one.
   - Useful for teams working on multiple branches (like develop, feature-x, main).
   - Supports PR builds (e.g., GitHub or GitLab pull requests).
   - **Use it when**: You're working with Git and want Jenkins to automatically handle builds for each branch.

This time, I will choose **Multibranch Pipeline**.

Add a display name and description for the project.

Set the repository source by clicking the branch source, select 'Git'.

Inside the Project Repository textbox, enter your git repository link. If the repository is not public, add credentials too.

Under Behaviors section, click the "Add" dropdown, and select "Filter by name (with wildcards)", and enter `*` inside the "Include" textbox.

In the Build Configuration Section, ensure that the Mode shows "by Jenkinsfile". Set the Jenkinsfile location (for example, `cicd/.jenkins/Jenkinsfile`) so that jenkins can know what to do when building your project.

Leave the other configurations as is, and click "Save" on the bottom side.