> This chapter should cover:
> - [Design a hybrid application]()
> - [Plan for session management in a distributed environment]()
> - [Plan web farms]()
> - [Run Microsoft Azure services on-premises with Azure Pack]()
> - [Enable deferred processing through Azure features including queues, scheduled and on-demand jobs, Azure Functions, and Azure Web Jobs]()

## Design a hybrid application

According to the exam ref, a hybrid application is one that is located in multiple locations, and uses the example of an application with both on-premise and Azure components.  The book identifies two patterns for hybrid applications: client-centric and system-centric. Other than noting that they are more prone to failure the book doesn't elaborate much on the client-centric patter.  It does, however, mention that systen-centric hybrid applications use a service bus to distribute requests, and includes a link to an MSDN tutorial on implementing a simple hybrid on-premise/Azure application.  Working through the tutorial is a useful exercise, I've shared my resulting code on Github: https://github.com/sabotuer99/AzureServiceBusDemo.

Some of the concerns to consider when planning a hybrid application:

 * Connections are not as reliable compared to an application that is located completely in one place
 * Authorization will need to be managed across multiple domains
 * Security of sensitive data is complicated when it is needed by a cloud based component.

## Plan for session management in a distributed environment

Before one can answer the question of HOW to manage state with sessions, it should first be ascertained to what degree the application is going to maintain state at all.  Whereas Web Forms tried to emulate desktop application statefulness by using the View State, MVC is geared more toward the inherently stateless nature of the web.  Rather than serializing everything into the View State, MVC aims to include relevant variables with every request, either as part of the route, in the query string, or in the request headers.

For occasions when it is necessary to maintain state, there are several options with MVC.  Hidden inputs, query string values, and cookies can all be used on the client side, and Application State, Session State, and Profiles can all be used on the server side. Using Session state in a distributed environment presents a unique challenge, because by default Session state is set to operate in InProc mode, which effectively limits the scope of the data to the individual web server.  To make InProc mode work in a distributed environment, it is necessary to create affinity between the client and the server, so that every request goes to the same server for the duration of the session.

The alternative to setting up affinity is to share session data across all the web servers.  There are two options that allow session data be shared by multiple machines: state server mode and sql server mode.

State server mode requires that the ASP.NET state service is running on the server being used to store the state information. Furthermore, the configuration section of the application's web.config file must include an entry setting the sessionState mode to "StateServer" and including the connection string to the state server, like so:

```
<configuration>
  <system.web>
    <sessionState mode="StateServer"
      stateConnectionString="tcpip=SampleStateServer:42424"
      cookieless="false"
      timeout="20"/>
  </system.web>
</configuration>
```
Sqlserver mode is similar, only instead of using a state server, the session information is persisted in a SQL database.  As such, this mode requires setting up a SQL session state database and adding the appropriate values to the web.config file:

```
<configuration>
  <system.web>
    <sessionState mode="SQLServer"
      sqlConnectionString="Integrated Security=SSPI;data 
        source=SampleSqlServer;" />
  </system.web>
</configuration>
```

## Plan web farms

A web farm is a group of multiple servers, usually a load balancer reroute to available servers.

Pros of using a web farm:
 * Reliability/availability
 * Capacity/performance
 * Scalability
   *  Azure App Service can automatically add or remove nodes at the request of the system administrator or automatically without human intervention.
 * Maintainability

In a web farm, app needs to configure a Data Protection and Caching strategie.

### Data Protection

### Caching

In a web farm the strategie about caching is to use a ditributed cache shared by multiple app servers.
it's:
 * Coherent
 * Survives server restarts and app deployments
 * Does't use local memory

## Run Microsoft Azure services on-premises with Azure Pack
## Enable deferred processing through Azure features including queues, scheduled and on-demand jobs, Azure Functions, and Azure Web Jobs
