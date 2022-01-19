## OpenShift & Hot Deploys

Testing an OpenShift `hot_deploy` on Travis CI. If your repository contains `.openshift/markers/hot_deploy`, when you perform a `git push` OpenShift will not rebuild the application and perform a hot deployment instead.

## Apache Tomcat/Lucene

Copy changed files into the running container. This is the fast way to update a container, however the target container must support hot deploy, too so that it makes sense. Most application servers like Tomcat/Lucene supports this.

## No hot deploy, okay I want a hot deploy

Alternatively you can use the following rhc in (RHEL) commands to enable and disable auto-deployment, and disable and enable hot deploys.

```bash
rhc app-configure <app> auto-deploy
rhc app-configure <app> no-auto-deploy
```

## Perform the following steps to enable hot deployment in OpenShift

Open the CLI, and navigate to the directory where you want to create the application. To create a new, let's say `Montanas HotDeployment` application, execute the following command. If you already have an OpenShift Java application, then you can work with that as well. Have a look at the following command: 

```openshift
rhc create-app myapp montanashotdeployment
```
To enable hot deployment, create a new file with the name  `hot_deploy` inside the `.openshift/markers` directory. On *nix* machines, you can create a new file using the touch command as shown in the following command. On Windows machines, you can use file explorer to create a new file. Have a look at the following code: 

```openshift
touch .openshift/markers/hot_deploy 
```

Add the new file to the Git repository index, commit it to the local repository, and then push the changes to the application's remote Git repository: 

```bash
git add .openshift/markers/hot_deploy $ git commit –am "enabled hot_deploy"
```

## OpenShift support of `hot_deploys`

OpenShift supports `hot deployment`. `Hot deployment` means that you can deploy your changes without the need to restart all the application cartridges (e.g. Apache, Mongo, etc.). 

The presence of the `hot_deploy` marker file informs OpenShift that you want to do hot deployment. Before stopping and starting the application cartridges, OpenShift checks for the existence of the `hot_deploy` marker file. For cartridges, hot deployment is achieved by using the deployment scanner. The scanner polls the deployments directory every 5 seconds to check for the existence of the WAR file. If the WAR file exists, it will undeploy the existing WAR file and deploy the new WAR file. 

You can configure the deployment scanner's scan-interval option in `.openshift/config/standalone.xml`: 
```xml
<subsystem xmlns="urn:jboss:domain:deployment-scanner:1.1"> <deployment-scanner path="deployments" relative-to="jboss.server.base.dir" scan-interval="5000" deployment-timeout="300" /> </subsystem>
```
## Conclusion

It is possible to deploy the generated `WAR` by manually copying the file to the deploy folder of the target. If the server is running, the `WAR` will deploy automatically via hot deploy. Otherwise, the `WAR` will deploy at the next start of the server.
