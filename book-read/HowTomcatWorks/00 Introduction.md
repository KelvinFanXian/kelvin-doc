# Override of Each Chapter

- Chapter 4 presents Tomcat 4's default connector. This connector has been deprecated in favor of a faster connector called Coyote. Nervertheless, the default connector is simpler an easier to understand.
- Chapter 5 discussed the container module. A container is represented by the org.apache.catalina.Container interface and there are four types of containers: engine, host, context, and wrapper.  This chapter offers two applications that work with contexts and wrappers.
- Chapter 6 explains the Lifecycle interface. This interface defines the lifecycle of a Catalina component and provides an elegant way of **notifying other components of events that occur in that component**. In addition, the Lifecycle interface provides an elegant mechanism for starting and stopping all the components in Catalina by one single start/stop.
- Chapter 7 convers loggers, which are components used for recording error messages and other messages.
- Chapter 8 explains about loaders. A loader is  an important Catalina module responsible for loading servlet and other classes that a web application users. This chapter also shows how application reloading is achieved.
- Chapter 9 discusses the manager, the component that manages sessions in session management. It explains the various types of managers and how a manager can persist session objects into a store. At the end of the Chapter, you will learn how to build an application that uses a StardardManager instance to run a servlet that uses session objects to store values.
- Chapter 10 convers web application security constranints for restricting access to certain contents. You will learn entities related to security such as principals, roles, login config, authenticators, etc. You will also write two applications that install an authenticator valve in the StandardContext object and uses basic authentication to authenticate users.
- Chapter 11 explains in detail the org.apache.catalina.core.StandardWrapper class that represents a servlet in a web application. In particular, this chapter explains how filters and a servlet's service method are invoked.  The application accompanying this chapter uses the StandWrapper instances to represents servlets.
- Chapter 12 covers the org.apache.catalina.core.StandardContext class that represents a web application.  In particular this chapter discusses how a StandardContext object is configured, what happens in it for each incoming HTTP request, how it supports automatic reloading, and how Tomcat 5 shares a thread that executes periodic tasks in its associated components.
- Chapter 13 presents the two other containers: host and engine. You can also find the standard implementation of these two containers:
  - org.apache.catalina.core.StandardHost
  - org.apache.catalina.core.StandardEngine.

- Chapter 14 offers the server and service components.  A server provides an elegant start and stop mechanism for the whole servlet container, a service serves as a holder for a container and one or more connectors. The application accompanying this chapter shows how to use a server and a service.
- Chapter 15 explains the configuration of a web application through Digester, an explicit open source project from the Apache Software Foundation. For those not initiated, this chapter presents a section that gently introduces digester library and how to use it to convert nodes in an XML document to Java objects. It then explains the ContextConfig object that configures a StandardContext instance.
- Chapter 16 explains the shutdown hook that Tomcat uses to always get a chance to do clean-up regardless how the user stops it (i.e. either appropriately by sending a shutdown command or inappropriately by simply closing the console.)
- Chapter 17 discusses the starting and stopping of Tomcat through the use of batch files and shell scripts.
- Chapter 18 presents the deployer, the components responsible for deploying and installing web applications.
- Chapter 19 discusses a special interface, ContainerServlet, to give a servlet access to the Catalina internal objects. In particlular, it discusses the Manager application that you can use to manage deployed applications.
- Chapter 20 discusses JMX and how Tomcat make its internal objects manageable by creating MBeans for those objects.

```css

```









