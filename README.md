Examples for Using Spring with GemFire Service for Cloud Foundry
----

This example demonstrates how to configure a GemFire service instance in Cloud Foundry using Spring configuration (Spring Application Context XML).

To accomplish this, you will need a Spring application context XML, which needs to be placed in an implementation jar, and applied to a GemFire service instance using `cf restart-gemire` command with `--spring-xml` and `--cluster-config` options of the GemFire CF CLI plugin, as in the following example:

```
  cf restart-gemfire <service-instance-name> --config=config.zip --spring-xml=cache-context.xml
```

Applying a new configuration to a cluster requires that the cluster be restarted. See [Using the GemFire for Cloud Foundry CLI Plug-in](http://docs.pivotal.io/gemfire-cf/gfe-cli.html) for more information on the GemFire CF CLI plugin.
 
The cluster config zip format follows a specific convention that requires a top directory called `cluster` and a sub-directory called `lib` that should contain all the implementation and dependency jars, with a Spring app context XML included in an implementation jar. 

A basic Spring app context XML is provided in `src/main/resources/cache-config.xml`. This example also provides the directories `cluster/lib` as a template to use for creating a cluster config zip file. As this approach uses Spring Context, the spring-context jar is already included in this directory as a dependency.

In order to build a new cluster config, follow the following steps:

1. Make sure the spring-context jar is included in the `cluster/lib` directory (already present).
1. Build the configuration using `buildClusterConfig` task.

    ```
    ./gradlew clean buildClusterConfig
    ```
    The end result of this step is a cluster configuration zip, `config.zip`, which you can find under `build/distributions`. It will have the following contents:
    
    ```
    $unzip -t build/distributions/config.zip 
	Archive:  build/distributions/config.zip
    testing: cluster/                 OK
    testing: cluster/lib/             OK
    testing: cluster/lib/cf-gemfire-spring-examples.jar   OK
    testing: cluster/lib/spring-context-3.2.12.RELEASE.jar   OK
No errors detected in compressed data of build/distributions/config.zip.

    ```
Note that the Spring app context XML file, `cache-context.xml` is included in the implementation jar, `cf-gemfire-spring-examples.jar`, as shown in the jar's table of contents:

	```
	$jar -tf cluster/lib/cf-gemfire-spring-examples.jar 
	META-INF/
	META-INF/MANIFEST.MF
	io/
	io/pivotal/
	io/pivotal/gemfire/
	io/pivotal/gemfire/example/
	io/pivotal/gemfire/example/SpringJavaBasedContainerGemFireConfiguration.class
	cache-context.xml

	```
	Note the complete path to the file in the jar (classpath), as it will be required in the next step.

1. Restart the GemFire service instance using the following command, where `--spring-xml` option takes the full path to the Spring app context XML in the containing jar: 

	```
	cf restart-gemfire <service instance name> --config=build/distributions/config.zip --spring-xml=cache-context.xml
	```

That's it. Your Spring configuration has been applied to the service instance.