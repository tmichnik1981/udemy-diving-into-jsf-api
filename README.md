# Diving into JSF API

#### Links

#### Notes and Commands

- Action listeners

  - attached to command buttons or links

  - called during invoke application phase or apply values phase

  - after execution of a.l.  jsf will call a method bound by the action attribute

  - action list. can modify the response returned by the action method

  - specifying

    -  the actionListener attribute
    - `<f:actionListener/>` tag
    - `<f:setPropertyActionListener/>` tag

  - method

    ```java
    //default
    public void actionListenerMethod(ActionEvent event){/*...*/}

    //without arguments
    public void actionListenerMethod(){/*...*/}

    //override ActionEvent and pass multiple arguments
    public void actionListenerMethod(Object o1, Object o2, ...){/*...*/}
    ```

    ```html
    <!-- Using actionListener attribute -->
     <h:commandButton value="Foo Listener" 
                                 actionListener="#{fooBean.actionListenerMethod}" 
                                 action="#{fooBean.actionMethod()}"/>
    <!-- Using f:actionListener and the binding attribute -->                             
     <h:commandButton value="Foo Listener" action="#{fooBean.actionMethod()}">
                    <f:actionListener binding="#{fooBean.actionListenerMethod()}" />
                </h:commandButton>

    <!-- Using f:actionListener and the type attribute -->
     <h:commandButton value="Foo Listener" action="#{fooBean.actionMethod()}">
                        <f:actionListener type="listeners.FooListener"/>
     </h:commandButton>    

    <!-- Using f:setPropertyActionListener -->
    <h:commandButton value="Foo listener" action="#{fooBean.actionMethod()}">
                    <f:setPropertyActionListener value="foo" target="#{fooBean.foo}"/>
    </h:commandButton>

     <!-- Using f:ajax and listener -->
     <h:commandButton value="Foo listener" action="#{fooBean.actionMethod()}">
                    <f:ajax listener="#{fooBean.ajaxActionListenerMethod}" />
     </h:commandButton>
    ```

- Application Action Listeners

  - application level
  - called by JSF event for command buttons/links
  - cannot invoke other listeners
  - cannot catch events from other listeners
  - propagate ActionEvent in the action listeners chain
  - eligible for injection
  - implement ActionListener or extend ActionListenerWrapper
  - propagate ActionEvent via `wrapped.processAction(event)`
  - declare the application action listener in **faces-config.xml**

  ```xml
  <application>                    
          <action-listener>listeners.ApplicationFooListenerWrapper</action-listener>
          <action-listener>listeners.ApplicationFooListener</action-listener>   
          <action-listener>listeners.ApplicationBuzzListener</action-listener>
      </application> 
  ```

  ```java
  public class ApplicationFooListener implements ActionListener {
     @Override
      public void processAction(ActionEvent event) throws AbortProcessingException {

          LOGGER.log(Level.INFO, "ApplicationFooListener#processAction() called ...");
          LOGGER.log(Level.INFO, "ApplicationFooListener#processAction(): the injected BuzzBean: {0}", buzzBean.getBuzz());
          actionListener.processAction(event);
      } 
  }
  ```


- Order of executions
  1. Explicit action listeners
  2. Application action listeners
  3. Action method


- System Events
  - javax.faces.event
  - subclasses of SystemEvent
- Component system events
  - javax.faces.event
  - subclasses of ComponentSystemEvent

```html
<h:form id="registerForm">            
<!--
	type - name of the event
	listener - evaluates an expression
-->
  <f:event listener="#{playerBean.validateAccount}" type="postValidate" />
</h:form> 
```

```java
public class ResourceListener implements SystemEventListener{
   @Override
    public void processEvent(SystemEvent event) throws AbortProcessingException {
    //...
    }
    @Override
    public boolean isListenerForSource(Object source) {
      	//we look for only UI events
        return (source instanceof UIViewRoot);
    }
}
```

```xml
<!--faces-config.xml -->
<application>       
	<system-event-listener>
         <system-event-listener-class>
                beans.ResourceListener
         </system-event-listener-class>
         <system-event-class>
                javax.faces.event.PreRenderViewEvent
         </system-event-class>
         <source-class>
                javax.faces.component.UIViewRoot
    	</source-class>
	</system-event-listener>
</application>
```

- subscribe to a system event programmatically
  - write a custom UIComponent
  - The componenet implements ComponentSystemEventListener
  - Subscribe to the PostAddToViewEvent system event
  - Override the processEvent() method



#### Instructions