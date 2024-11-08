# Experience Continuous Integration with Jenkins | Ansible | Artifactory | SonarQube | PHP

## Simulating a typical CI/CD Pipeline for a PHP Based application

**In this Project, I will create a pipeline that  simulates continuous integration and delivery. Target end to end CI/CD pipeline is represented by the diagram below.**

![format](https://github.com/user-attachments/assets/f050b19e-8adb-4983-b161-d99f85367526)

It is important to know that both `Tooling` and `TODO` Web Applications (the applications that will be used in this project) are based on an interpreted (scripting) language (PHP). It means, it can be deployed directly onto a server and will work without compiling the code to a machine language.

The problem with that approach is, it would be difficult to package and version the software for different releases. And so, in this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible `uri module`.

















