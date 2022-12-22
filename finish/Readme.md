This code is to test "ALL AUTHENTICATED" role in context of JAX-RS resource.

In the web.xml there is constraint that should protect all resources defined like this:

```
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>all-resources</web-resource-name>
            <url-pattern>/*</url-pattern>
            <http-method>GET</http-method>
            <http-method>POST</http-method>
        </web-resource-collection>
        <auth-constraint>
            <role-name>auth-user</role-name>
        </auth-constraint>
    </security-constraint>
    <security-role>
        <role-name>auth-user</role-name>
    </security-role>    

```

There is matching binding in the application definition in server.xml:

```
  <webApplication location="system.war" contextRoot="/">
    <!-- remove this binding for token to work , and remove constraints in web.xml -->
    <application-bnd>
    <security-role name="auth-user">
      <special-subject type="ALL_AUTHENTICATED_USERS" />
    </security-role>
  </application-bnd> 
  </webApplication>
```

Although user is correctly authenticated via `frontend` app and sends JWT token, he is not considered as authenticated and app fails with 403 displayed in the frontend app:
```
 Exception thrown by application class 'jakarta.faces.webapp.FacesServlet.service:236'
jakarta.servlet.ServletException: Unknown error, status code 403
at jakarta.faces.webapp.FacesServlet.service(FacesServlet.java:236)
at [internal classes]
at io.openliberty.guides.frontend.filters.NoCacheFilter.doFilter(NoCacheFilter.java:38)
at com.ibm.ws.webcontainer.filter.FilterInstanceWrapper.doFilter(FilterInstanceWrapper.java:201)
at [internal classes]
Caused by: jakarta.ws.rs.WebApplicationException: Unknown error, status code 403
at org.jboss.resteasy.microprofile.client.DefaultResponseExceptionMapper.toThrowable(DefaultResponseExceptionMapper.java:21)
at [internal classes]
at com.sun.proxy.$Proxy78.getUsername(Unknown Source)
at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.base/java.lang.reflect.Method.invoke(Method.java:566)
at org.jboss.weld.bean.proxy.AbstractBeanInstance.invoke(AbstractBeanInstance.java:38)
at org.jboss.weld.bean.proxy.ProxyMethodHandler.invoke(ProxyMethodHandler.java:106)
at io.openliberty.guides.frontend.client.SystemClient$8711121$Proxy$_$$_WeldClientProxy.getUsername(Unknown Source)
at io.openliberty.guides.frontend.ApplicationBean.getUsername(ApplicationBean.java:62)
at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.base/java.lang.reflect.Method.invoke(Method.java:566)
at jakarta.el.BeanELResolver.getValue(BeanELResolver.java:85)
... 4 more
```