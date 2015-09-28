Examples for using Spring in the GemFire service for CloudFoundry
----

GemFire provides the ability to bootstrap itself by delegating to Spring Data GemFire during startup.
This is done using the `--spring-xml-location` option to `gfsh`. For example:
```
gfsh start server --name=server1 --classpath=my-spring-code.jar \
    --spring-xml-location=spring-gemfire-context.xml
```

In this case the file referenced by `spring-xml-location` is searched for as a classpath resource.
As such, it would typically be delivered in a jar referenced by the `classpath` option.

For the GemFire CloudFoundry service, this functionality is exposed using the `--spring-xml`
option when using the GemFire CF cli plugin. See ... for more information on the plugin.

The following examples expand on the examples provided in [spring-data-gemfire-exmaples](https://github.com/spring-projects/spring-gemfire-examples).

Specifically we will use the example provided in basic/java-config as a base. And follow below steps:

1. Add a minimal XML

Currently GemFire can be bootstrapped using Spring XML. In order to use Spring's Java configuration
we need a minimal Spring XML config as shown below.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

  <!-- To import your exiting GemFire Java Configuration -->
  <context:component-scan base-package="org.springframework.data.gemfire.example"/>
</beans>
```
For the basic/java-config project, this XML should be added under a `src/main/resources` directory
as `cache-context.xml`. This will include the XML into the jar during the Gradle `build` step.

1. The directory `cluster` provides a template to use for creating a cluster config zip file. In order
to scan packages for `@Configuration` annotation this template includes a `spring-context` jar in
the `cluster/lib` directory.

1. Add `java-config.1.6.0.RELEASE.jar` present under basic/java-config/build/libs to the cluster config template under `cluster\lib`

1. Create a zip file with the command `zip -r config.zip cluster/`

1. Deploy using the following `cf` command: `cf restart-gemfire <service name> --config=config.zip --spring-xml=cache-context.xml`
 
 
 
 Using this example as a base
perform the following steps in order to deploy it to a GemFire CloudFoundry service instance:

- provide spring context xml in resources
- bar
