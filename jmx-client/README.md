This example shows how to create the JMX client to communicate the spring-boot actuator endpoints of JMX. 


### Creating an RMI Connector Client
The Client class creates an RMI connector client that is configured to connect to an RMI connector server that you will launch when you start the JMX agent, Main. This will allow the JMX client to interact with the JMX agent as if they were running on the same machine.

```java
echo("\nCreate an RMI connector client and " +
    "connect it to the RMI connector server");
JMXServiceURL url = new JMXServiceURL("service:jmx:rmi:///jndi/rmi://:56573/jmxrmi");
JMXConnector jmxc = JMXConnectorFactory.connect(url, null);
```

### Connecting to the Remote MBean Server
With the RMI connection in place, the JMX client must connect to the remote MBean server, so that it can interact with the various MBeans registered in it by the remote JMX agent.

```java
MBeanServerConnection mbsc = 
    jmxc.getMBeanServerConnection();
```

### List the domains

```java
  System.out.println("\nDomains:");
  String domains[] = mbsc.getDomains();

  for (String domain : domains) {
          System.out.println("\tDomain = " + domain);
  }
```

### Invoke the Info Endpoint

```java
   ObjectName mbeanName = new ObjectName(name);

   data = (Map) mbsc.invoke(mbeanName, "health", null, null);

   System.out.println(data);
```

### Invoke the Metric Endpoint for pulling the ```jvm.memory.max```

```java
  data = (Map) mbsc.invoke(mbeanName, "metric", new Object[] { "jvm.memory.max" },
                                        new String[] { String.class.getName() });

  System.out.println(data);
```

#### Reference
* [Oracle Doc](https://docs.oracle.com/javase/tutorial/jmx/remote/custom.html)


