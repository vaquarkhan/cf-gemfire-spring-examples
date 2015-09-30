Examples for using Spring in the GemFire service for CloudFoundry
----

This example demonstrates how can we configure a GemFire service with-in cloud-foundry using spring configuration.

You can use `--spring-xml` option when using the GemFire CF cli plugin. See [GemFire-CLI](http://docs.pivotal.io/gemfire-cf/gfe-cli.html) for more information on the plugin.

```
  cf restart-gemfire <service-name> --config=config.zip --spring-xml=cache-context.xml
```

Currently GemFire can be bootstrapped using only Spring XML. In order to use Spring's Java configuration
we need a minimal Spring XML config in-order to initialize Spring context and then to load Java configuration. 
You can find the minimal XML under 'src/main/resources' as cache-config.xml.

In order to build a new cluster config, follow these steps:

1. This example provides a directory `cluster` as a template to use for creating a cluster config zip file. In order
to scan packages for `@Configuration` annotation this template includes a `spring-context` jar in
the `cluster/lib` directory.

1. Build the configuration using 'buildClusterConfig' task.

    ```
    ./gradlew clean buildClusterConfig
    ```
    You can find the cluster configuration 'config.zip' under 'build/distributions'.

1. Restart GemFire service instance using the following `cf` command: `cf restart-gemfire <service name> --config=config.zip --spring-xml=cache-context.xml`
