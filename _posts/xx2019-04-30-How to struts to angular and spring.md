---
layout: post
author: HM
---

*Some notes on what I've been doing because one day I'll forget them all. I wanna title this __HM BRIEFLY EXPLAINS CONVERTING STRUTS 1.x TO ANGULAR & SPRING MVC__*


Inside the WEB-INF folder there will be a 
1. __struts-config__ folder with various __struts-config.xml__. containing the action mapping and form beans
2. __command-config__ folder with various __command-config.xml__ containing command class methods that are called by the action
3. __tiles-def__ folder with various __tiles-def.xml__ containing the mapping of forward path to jsp file
4. __pfw__ is the db sql stuff.


If you don't already know the application that is in struts, start by mapping out the page flow of the application. Find the first page of the application (.jsp or .do). 

*I suggest opening the project in sublime or whatever that lets you "go to definition"*

E.g. if my main page has 2 options, one to enquire an application and one to submit an application, there will be 2 flows that i will map out separately. 
You just want to see what classes and methods are being used.

### 1. Find and map out the flow of your application ###
 
E.g. the first page of the application main menu (mainmenu.jsp) has a few links, one to enquire an application.

```jsp
<a href='<session:constant name="ContextPath"/>/enquireAnApplication.do'>
```

Find this enquireAnApplication.do action in the struts-config.xml. Find /enquireAnApplication.
```xml
<action path="/enquireAnApplication"
	name="ApplicationEnquiryForm"
	type="com.xxx.action.LoadApplicationForm"
	parameter="forwardSuccess=true"			
	scope="request">
	<forward name="success" path=".xxx.xxx.enquire.appln" />
	<forward name="failure" path=".xxx.xxx.invalidAccessError" />
</action>		
```
> type="com.xxx.action.LoadApplicationForm" 


__LoadApplicationForm.java__ is an action class inside the folder structure com.xxx.action. The methods inside will be executed. Or you can right click on the name and you can "Go to definition" which will open the class for you. 

> /enquireAnApplication

Look for this string inside the corresponding __command-config.xml__ file. E.g. if the action falls in struts-config-enquiry.xml then the command should fall in command-config-enquiry.xml. Or you can right click on the name and you can "Go to definition" which will open the class for you. 

```xml
<command name="/enquireAnApplication"
	type="com.xx.command.EnquiryCommand"
	service="getApplicationEnquiry">
</command>
```

> type="com.xx.command.EnquiryCommand"


 __EnquiryCommand.java__ will be inside the folder structure com.xx.command. The method name __getApplicationEnquiry__ will be executed.

 Not all /xxx.do have a corresponding command to be executed. If the action /xxx can't be found in the command-config.xml, it means that there is only the action, no command method. 


>  forward path=".xxx.xxx.enquire.appln"


This is the path to search for in the corresponding __tiles-def__ file to find out which .jsp file it will route to upon success/failure. 

```xml
<definition name=".xxx.xxx.enquire.appln" extends=".xxx.xxx">
	<put name="main" value="/jsp/EnquireApp.jsp" />
</definition>
```

__EnquireApp.jsp__ is the next page you will route to.

Open the .jsp page, see what it does, find the .do method that it calls, repeat from the top to find and map all the classes and methods that are being called as the application go from page to page :)


---
### 2. Recreate the application flow on angular

Create a new project on angular: https://angular.io/guide/quickstart

Go through the tutorial if you don't know angular yet.

For each application flow, I usually create:
- a module
- a component for each page
- a routing module
- a service 

then import all these into the main app.module.ts and app-routing.module.

Briefly do up/link up all the pages so that it looks like the original application (without all the logic behind, just routerlink page to page by clicking a button)


---
### 3. Create new maven project ###

web.xml  
MyDispatcherServlet extends DispatcherServlet  
Custom Filter implements Filter  
MyServlet extends HttpServlet  

servlet-context.xml  
```xml <context:component-scan base-package="whereyourrestcontrollersare" />```


---
### 4. Create new form objects to replace form-beans ###

Original form on struts-config.xml: 
```xml
<form-bean name="ApplicationEnquiryForm" type="org.apache.struts.validator.DynaValidatorActionForm">
	<form-property name="applStatus" type="java.lang.String" />
	<form-property name="periodFrom" type="java.lang.String" />
	<form-property name="periodTo" type="java.lang.String" />
    ...
    ...

```

Create a new object on angular

```typescript
export class ApplicationEnquiryForm {

    public applStatus: string;
    public periodFrom: string;
    public periodTo: string;
    ...
    ...
```

and similar on Java side 

```java
public class ApplicationEnquiryForm {

    private String applStatus;
    private String periodFrom;
    private String periodTo;
    ...
    ...
    with their getters and setters
```

as you will pass the object from frontend (angular) to backend (java). This allows the object to auto map the fields inside (I think)

---


### 5. Link up angular side to spring controllers

To send/retrieve ApplicationEnquiryForm to/from the backend, send HTTP GET/POST request. Some sniplets that I hope are useful:

EnquireApplication.component.ts:
```typescript
getApplicationEnquiryForm() {
    this.enquireApplication.getApplicationEnquiryForm().then(returnedForm => {
    this.applicationEnquiryForm = returnedForm;
    });
}
```

EnquireApplication.service.ts:

``` typescript
  getApplicationEnquiryForm() {
    let url = '/getApplicationEnquiryForm'
    return this.restclientService.getrawjson(url);
  }

  postApplicationEnquiryForm(applicationEnquiryForm: ApplicationEnquiryForm) {
    let url = '/postApplicationEnquiryForm'
    return this.restclientService.postjson(url, applicationEnquiryForm);
  }
```

ApplicationEnquiryController.java

``` java

@RestController
public class EnquirePrintController {

    @RequestMapping(value = "/getApplicationEnquiryForm", method = RequestMethod.GET)
    public ApplicationEnquiryForm getApplicationEnquiryForm(HttpServletRequest request) {

    ApplicationEnquiryForm applicationEnquiryForm = (ApplicationEnquiryForm) request.getSession().getAttribute("APPLICATIONENQUIRYFORM");

        
    return applicationEnquiryForm;
    }

    ...
    ...

```

My common shared restclientservice looks something like this:
``` typescript
@Injectable()
export class RestclientService {

  headerDict = {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
    'Access-Control-Allow-Headers': 'Content-Type',
  }

  headerObj = {
    headers: new Headers(this.headerDict),
  };

  preUrl = "/app";

  constructor(private http: Http) { }

  getrawjson(url: string): Promise<any> {
    return this.http
      .get(this.preUrl + url)
      .toPromise().then(res => res.json());
  }
  
  postjson(url: string, rawdata: any): Promise<any> {
    var data = JSON.stringify(rawdata);
    return this.http
      .post(this.preUrl + url, data, this.headerObj)
      .toPromise()
      .then(res => res.json() as any);
  }

  ...
  ...

```

If you can send your form object back and retrieve the fields then yay.

---

### 6. Convert all your .jsp pages to .html and .ts
Bring over all validations/javascript parts to typescript. 

Convert jsp tags to angular example:
- c:choose c:when c:otherwise to ng-container *ngIf else etc.  

---

### 7. Do up backend flow with the action and command methods used ###

 
Following the flow mapped out,

Create a new class e.g. ApplicationEnquiryCommand.java to contain all the logic (from struts action & command) to be processed for that Enquiry flow. 

Take out all the methods being called in the action and command for each page and throw them in there. Then call the methods accordingly from your REST Controller to process the forms. 

For struts __action__ classes:
If session is used, change session to the new form object created. Some action classes don't do much, they only put objects into the session/Icontext etc. You don't need to do that anymore as you are filling up our form object on the page itself.


``` java
@RequestMapping(value = "/postApplicationEnquiryForm", method = RequestMethod.POST)
    public ApplicationEnquiryForm submitApplicationEnquiryForm(@RequestBody ApplicationEnquiryForm applicationEnquiryForm, HttpServletRequest request) {

     ApplicationEnquiryCommand applicationEnquiryCommand = new ApplicationEnquiryCommand();
    applicationEnquiryCommand.getApplicationEnquiryList(applicationEnquiryForm);

```

---

### 8. Spring security stuff

What I did for own application login:

Security.xml  
BasicAuthConfiguration.java  
CustomAuthenticationProvider.java    
Authentication in request header at angular side.  
@RequestMapping("/login") at LoginController.java  

For login from somewhere else and redirected to your application:  

CustomAuthenticationEntryPoint implements AuthenticationEntryPoint  
ReqAuthFilter extends AbstractPreAuthenticatedProcessingFilter  
CustomAuthenticationProvider implements AuthenticationProvider  


---

*Glad to have been through this all. From not knowing a single thing about struts & angular to being able to help others with it.*

