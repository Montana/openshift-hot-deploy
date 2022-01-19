# openshift-hot-deploy
Testing an OpenShift `hot_deploy` on Travis CI.

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
git add .openshift/markers/hot_deploy $ git commit –am "enabled hot deployment
```

## OpenShift Support of `hot_deploys`

OpenShift supports `hot deployment`. `Hot deployment` means that you can deploy your changes without the need to restart all the application cartridges (e.g. Apache, Mongo, etc.). 

The presence of the `hot_deploy` marker file informs OpenShift that you want to do hot deployment. Before stopping and starting the application cartridges, OpenShift checks for the existence of the `hot_deploy` marker file. For cartridges, hot deployment is achieved by using the deployment scanner. The scanner polls the deployments directory every 5 seconds to check for the existence of the WAR file. If the WAR file exists, it will undeploy the existing WAR file and deploy the new WAR file. 

You can configure the deployment scanner's scan-interval option in `.openshift/config/standalone.xml`: 
```xml
<subsystem xmlns="urn:jboss:domain:deployment-scanner:1.1"> <deployment-scanner path="deployments" relative-to="jboss.server.base.dir" scan-interval="5000" deployment-timeout="300" /> </subsystem>
```
