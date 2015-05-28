---
layout: post
title: Swagger with Websphere 8.5.5 and ArrayIndexOutOfBoundsException 
---

Recently when I tried to deploy an app and I got below stack trace: 

{% highlight xml %}
[5/27/15 13:44:03:419 EDT] 00000001 ContainerHelp E   WSVR0501E: Error creating component com.ibm.ws.runtime.component.CompositionUnitMgrImpl@c2dcd39
com.ibm.ws.exception.RuntimeWarning: com.ibm.ws.webcontainer.exception.WebAppNotLoadedException: Failed to load webapp: Failed to load webapp: java.lang.ArrayIndexOutOfBoundsException
        at com.ibm.ws.webcontainer.component.WebContainerImpl.install(WebContainerImpl.java:432)
        at com.ibm.ws.webcontainer.component.WebContainerImpl.start(WebContainerImpl.java:718)
        at com.ibm.ws.runtime.component.ApplicationMgrImpl.start(ApplicationMgrImpl.java:1177)
        at com.ibm.ws.runtime.component.DeployedApplicationImpl.fireDeployedObjectStart(DeployedApplicationImpl.java:1370)
        at com.ibm.ws.runtime.component.DeployedModuleImpl.start(DeployedModuleImpl.java:639)
        at com.ibm.ws.runtime.component.DeployedApplicationImpl.start(DeployedApplicationImpl.java:968)
        at com.ibm.ws.runtime.component.ApplicationMgrImpl.startApplication(ApplicationMgrImpl.java:776)
        at com.ibm.ws.runtime.component.ApplicationMgrImpl$5.run(ApplicationMgrImpl.java:2195)
        at com.ibm.ws.security.auth.ContextManagerImpl.runAs(ContextManagerImpl.java:5477)
        at com.ibm.ws.security.auth.ContextManagerImpl.runAsSystem(ContextManagerImpl.java:5603)
        at com.ibm.ws.security.core.SecurityContext.runAsSystem(SecurityContext.java:255)
        at com.ibm.ws.runtime.component.ApplicationMgrImpl.start(ApplicationMgrImpl.java:2200)
        at com.ibm.ws.runtime.component.CompositionUnitMgrImpl.start(CompositionUnitMgrImpl.java:435)
        at com.ibm.ws.runtime.component.CompositionUnitImpl.start(CompositionUnitImpl.java:123)
        at com.ibm.ws.runtime.component.CompositionUnitMgrImpl.start(CompositionUnitMgrImpl.java:378)
        at com.ibm.ws.runtime.component.CompositionUnitMgrImpl.access$500(CompositionUnitMgrImpl.java:126)
        at com.ibm.ws.runtime.component.CompositionUnitMgrImpl$CUInitializer.run(CompositionUnitMgrImpl.java:984)
        at com.ibm.wsspi.runtime.component.WsComponentImpl$_AsynchInitializer.run(WsComponentImpl.java:502)
        at com.ibm.ws.util.ThreadPool$Worker.run(ThreadPool.java:1865)
Caused by: com.ibm.ws.webcontainer.exception.WebAppNotLoadedException: Failed to load webapp: Failed to load webapp: java.lang.ArrayIndexOutOfBoundsException
        at com.ibm.ws.webcontainer.WSWebContainer.addWebApp(WSWebContainer.java:759)
        at com.ibm.ws.webcontainer.WSWebContainer.addWebApplication(WSWebContainer.java:634)
        at com.ibm.ws.webcontainer.component.WebContainerImpl.install(WebContainerImpl.java:426)
        ... 18 more
Caused by: com.ibm.ws.webcontainer.exception.WebAppNotLoadedException: Failed to load webapp: java.lang.ArrayIndexOutOfBoundsException
        at com.ibm.ws.webcontainer.VirtualHostImpl.addWebApplication(VirtualHostImpl.java:176)
        at com.ibm.ws.webcontainer.WSWebContainer.addWebApp(WSWebContainer.java:749)
        ... 20 more
Caused by: java.lang.RuntimeException: java.lang.ArrayIndexOutOfBoundsException
        at org.apache.webbeans.portable.AnnotatedElementFactory.newAnnotatedType(AnnotatedElementFactory.java:150)
        at org.apache.webbeans.config.BeansDeployer.deployFromClassPath(BeansDeployer.java:484)
        at org.apache.webbeans.config.BeansDeployer.deploy(BeansDeployer.java:171)
        at org.apache.webbeans.lifecycle.AbstractLifeCycle.startApplication(AbstractLifeCycle.java:124)
        at org.apache.webbeans.web.lifecycle.WebContainerLifecycle.startApplication(WebContainerLifecycle.java:78)
        at com.ibm.ws.webbeans.common.CommonLifeCycle.startApplication(CommonLifeCycle.java:106)
        at com.ibm.ws.webbeans.services.JCDIServletContainerInitializer.onStartup(JCDIServletContainerInitializer.java:85)
        at com.ibm.ws.webcontainer.webapp.WebAppImpl.initializeServletContainerInitializers(WebAppImpl.java:613)
        at com.ibm.ws.webcontainer.webapp.WebAppImpl.initialize(WebAppImpl.java:409)
        at com.ibm.ws.webcontainer.webapp.WebGroupImpl.addWebApplication(WebGroupImpl.java:88)
        at com.ibm.ws.webcontainer.VirtualHostImpl.addWebApplication(VirtualHostImpl.java:169)
        ... 21 more
Caused by: java.lang.ArrayIndexOutOfBoundsException
        at org.apache.webbeans.portable.AbstractAnnotatedCallable.setAnnotatedParameters(AbstractAnnotatedCallable.java:66)
        at org.apache.webbeans.portable.AnnotatedConstructorImpl.<init>(AnnotatedConstructorImpl.java:56)
        at org.apache.webbeans.portable.AnnotatedElementFactory.newAnnotatedType(AnnotatedElementFactory.java:117)
        ... 31 more
        
  {% endhighlight %}      

Let me give you some context first. Application container is Websphere 8.5.5 and application is a very simple Spring web app with only one REST controller 
and dependency to the swagger. The last thing, as it turned out, is crucial in the whole case. Swagger dependency: 
  
{% highlight xml %}

<dependency>
  <groupId>com.mangofactory</groupId>
  <artifactId>swagger-springmvc</artifactId>
  <version>0.8.8</version>
</dependency>
        
{% endhighlight %}

contains a dependency to guava version 15. This version of guava contains a workaround to make it compatible with CDI used
in JEE7 containers but breaks CDI 1.0 used by JEE6 containers like Websphere 8.5.5 which I'm using. Here you can find comment about
the issue: https://code.google.com/p/guava-libraries/wiki/Release15#A_note_on_JEE6_/_CDI_1.0 
The simplest solution is just to add a newer guava version like this:

{% highlight xml %}
 
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>18.0</version>
</dependency>
        
{% endhighlight %} 


