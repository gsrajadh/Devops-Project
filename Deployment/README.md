## Milestone 3 - Deployment

 In this milestone we have automated the deployment of our project, Web Application [Direwolf](https://github.ncsu.edu/mpancha/Direwolf) in production environment.

### Setup

For this milestone, we have created two environments: Canary and Production. These correspond to Release and Dev branches in our deployment strategy respectively.

![](https://github.com/gsrajadh/Devops-Project/blob/master/Deployment/screenshots/setup.png)

### Capabilities

* #### Automatic configuration of production environment
 [configure.py](https://github.com/gsrajadh/Devops-Project/blob/master/Deployment/scripts/configure.py) script is our infrastructure management module. This module automatically creates AWS instance for production and canary environment. Using ansible playbook, the module installs docker for App container and New Relic for monitoring on these instances.

* ### Deploy software to the production environment
Git commit to dev branch will automatically trigger Jenkins build and testing process using post-commit hook. If build and testing is successful, the application is automatically deployed in canary environment using ansible. The idea here is to test the dev branch changes on canary environment and merge the changes to release branch if suitable. Whenever a change is pushed to release branch on github repository, it will trigger build and testing. On the success of it, application will be deployed to the production environment. We have achieved this using post receive hooks(web hooks) and ansible tasks.

* #### Screencast:
[Demonstration of configuration and deployment in production environment and proxy server](https://vimeo.com/146164018)


* ### Feature flags
We have configured feature flags using redis. When flag is set, admin user cannot be created and when the flag is not using admin user can be created.
* #### Screencast:
[Feature Flag Demo](https://www.youtube.com/watch?v=KYcThmDIB44&edit=vd)

* ### Monitoring deployed application
We have used  New Relic to monitor the application we have deployed. When the CPU usage increases above 20% or when memory usage increases above 30%, an email is sent as an alert. We are also monitoring application using newrelic ruby gem.

Canary Monitoring  

![](https://github.com/gsrajadh/Devops-Project/blob/master/Deployment/screenshots/Screen%20Shot%202015-11-18%20at%207.28.20%20PM.png)

Email Notification   

![](https://github.com/gsrajadh/Devops-Project/blob/master/Deployment/screenshots/Screen%20Shot%202015-11-18%20at%207.28.53%20PM.png)

* ### Canary Release
We have used proxy server to route 10% of the traffic to the canary and remaining traffic to the stable environment. The script for this is  [main.js](https://github.com/gsrajadh/Devops-Project/blob/master/Deployment/scripts/main.js). We have used web hooks feature of New Relic to stop routing traffic to canary if an alert is received and send all the traffic to production environment.  

* #### Screencast:  
[Demonstration of monitoring and disabling traffic to canary on receiving an alert](https://www.youtube.com/watch?v=73Zdck7cYvM&feature=youtu.be)  
Please note: Alerts are sent after 5 minutes if the usage is above threshold. As that would be a long waiting time, for this video we have used test alert to demonstrate that canary traffic is disabled when an alert is sent. However, we have attached screenshots above to demonstrate that an alert mail is sent when the threshold is crossed.

* ### References and Credits:  
1. Docker installation ansible role
https://github.com/angstwad/docker.ubuntu/
2. New Relic installation ansible role for basic installation
https://github.com/sivel/ansible-newrelic
3. AWS instance creation API wrapper
https://github.com/mpancha/hw1
