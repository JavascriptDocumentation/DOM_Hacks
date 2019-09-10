* text node represents white space
* Window object is the parent object of the browser
( )

* `document.getElementsByClassName('slds-file-selector__button slds-button slds-button_neutral')`


* Generally, user `querySelector` and `querySelectorAll` 

* Looping through list items:
```javascript
const items = document.querySelectorAll('.items');

items.forEach((item) => console.log(item));
```

```javascript
const ul = document.querySelector('.items');

ul.remove();
```

* [firselementchild](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/firstElementChild)

* <b>Use This for changing color or background related to events</b>:
```javascript
const s = document.querySelector('.slds-file-selector__body');
s.style.background = 'red';

s.addEventListener('event', (e) => {
    
    e.preventDefault();
    console.log('click');
})
```

^^ the first is the event the second is the function 

* https://www.freecodecamp.org/forum/t/push-a-new-local-branch-to-a-remote-git-repository-and-track-it-too/13222

https://developer.mozilla.org/en-US/docs/Web/API/Event

https://www.w3.org/TR/CSS2/cascade.html

https://codepen.io/bradtraversy/pen/Bwapow

https://www.youtube.com/watch?v=l1-F5_w4l-0

https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Methods_Index

https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/firstElementChild

https://developer.mozilla.org/en-US/docs/Web/Events

https://developer.mozilla.org/en-US/docs/Web/API/Event

https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference_force_auth.htm#cli_reference_web_login

## Lightning Aura Components
* Lightning components are supposed to be self-contained. 
* They are stand-alone elements that encapsulate all of their essential functionality. 
* A component is not allowed to reach into another component, even a child component, and alter its internals.

There are two principal ways to interact with or affect another component:
    * setting attributes on the component’s tag.  A component’s public attributes constitute one part of its API.
    *  interact with a component is through events
        * Like attributes, components declare the events they send out, and the events they can handle.
        * Like attributes, these public events constitute a part of the component’s public API. 

When you set the onclick attribute on a <lightning:button> to an action handler in a component’s controller, you create a direct relationship between those two components. 

```html
<lightning:input type="toggle" 
            label="Reimbursed?"
            name="reimbursed"
            class="slds-p-around--small"
            checked="{!v.expense.Reimbursed__c}"
            messageToggleActive="Yes"
            messageToggleInactive="No"
            onchange="{!c.clickReimbursed}"/>
```
* `type="toggle"` is a checkbox with a toggle switch design
* `messageToggleActive` and `messageToggleInactive` provides a custom label for the checked and unchecked positions.
* `onchange` attribute of `<lightning:input>` a way to wire the toggle switch to an action handler that updates the record when you slide right (checked) or slide left (unchecked).
* Components do not reach into other components and set values on them. 
```javascript
({
    clickReimbursed: function(component, event, helper) {
        var expense = component.get("v.expense");
        var updateEvent = component.getEvent("updateExpense");
        updateEvent.setParams({ "expense": expense });
        updateEvent.fire();
    }
})
```
#### Sequence of events:
1. Get the expense item that changed.
2. Create a server action to update the underlying expense record.
3. Package expense into the action.
4. Set up a callback to handle the response.
5. Fire the action, sending the request to the server.
6. When the response comes and the callback runs, update the expenses attribute.

### Event Handling in Base Lightning Components
To access DOM elements for base components via `event.target` or `event.currentTarget` breaks encapsulation because it provides access to another component's DOM elements.
* Lightning Locker service enforces encapsulation.  Use the methods described to make code compliant with Lightning Locker.
* To retrieve the component that fired the event use: `event.getSource()`
```xml
<aura:component>
    <lightning:button name="myButton" onclick="{!c.doSomething}"/>
</aura:component>
```
```javascript
({
    doSomething: function(cmp, event, helper) {
        var button = event.getSource();

        //The following patterns are not supported
        //when you’re trying to access another component’s
        //DOM elements.
        var el = event.target;
        var currentEl = event.currentTarget;
    }
})
```
Retrieve a component attribute that’s passed to the event by using this syntax:
`event.getSource().get("v.name")`

* `event.getSource()` helps you determine which component fired an event.
* To retrieve the name of the button that fired the event, use `event.getSource().get("v.name")`.
```xml
<aura:component>
    <lightning:button label="New Record" name="new" onclick="{!c.handleClick}"/>
    <lightning:button label="Edit" name="edit" onclick="{!c.handleClick}"/>
    <lightning:button label="Delete" name="delete" onclick="{!c.handleClick}"/>
</aura:component>
```
```javascript
({
    handleClick: function(cmp, event, helper) {
        //returns "new", "edit", or "delete"
        var buttonName = event.getSource().get("v.name");
    }
})
```
Most base lightning components provide a `class` attribute, so you can add a utility class or custom class to the outer element of the components.
#### Apply a spacing utility class to `lightning:button`:
```xml
<lightning:button name="submit" label="Submit" class="slds-m-around_medium"/>
```
## CSS in Components
 * https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/components_css.htm
 There’s no space in the .THIS.toggle selector because we’re using the rule to match a <p> tag, which is a top-level element.
 * `$A.util.toggleClass(cmp, 'class')`

### Conditionally Create Elements with <aura:if>
 ```html
 <aura:if isTrue="{!v.isError}">
    <div>{!v.errorMessage}</div>
</aura:if>
```
The `<div>` component and its contents are only created and rendered if the value of the `isTrue` expression evaluates to true. 
If the value of the `isTrue` expression changes and evaluates to false, all the components inside the `<aura:if>` tag are destroyed.    
The general guideline is to use `<aura:if>` because it helps your components load faster initially by deferring the creation and rendering of the enclosed element tree until the condition is fulfilled.
#### Toggle Visibility Using CSS
You can use CSS to toggle visibility of markup by calling `$A.util.toggleClass(cmp, 'class')` in JavaScript code.
* Elements in markup are created and rendered up front, but they’re hidden. 
### Handling Events with Client-Side Controllers
* A client-side controller handles events within a component.
* It’s a JavaScript resource that defines the functions for all of the component’s actions.
* A client-side controller is a JavaScript object in object-literal notation containing a map of name-value pairs.
* an array of key:value pairs, with a colon separating the keys and values, and a comma after every key:value pair, except for the last, just like a regular array.
* Each name corresponds to a client-side action. 
* Its value is the function code associated with the action.
* Each action function takes in three parameters:
    * cmp—The component to which the controller belongs.
    * event—The event that the action is handling.
    * helper—The component’s helper, which is optional. A helper contains functions that can be reused by any JavaScript code in the component bundle.

```xml
<aura:component>
    <aura:attribute name="text" type="String" default="Just a string. Waiting for change."/>
    <input type="button" value="Flawed HTML Button"
        onclick="alert('this will not work')"/>
    <br/>
    <lightning:button label="Framework Button" onclick="{!c.handleClick}"/>
    <br/>
    {!v.text}
</aura:component>
```

### Adding and Removing Styles
* To retrieve the class name on a component, use `component.find('myCmp').get('v.class')`, where myCmp is the `aura:id` attribute value.
* To append and remove CSS classes from a component or element, use the `$A.util.addClass(cmpTarget, 'class')` and `$A.util.removeClass(cmpTarget, 'class')` methods.

```xml
<aura:component>
    <div aura:id="changeIt">Change Me!</div><br />
    <lightning:button onclick="{!c.applyCSS}" label="Add Style" />
    <lightning:button onclick="{!c.removeCSS}" label="Remove Style" />
</aura:component>
```

```css
.THIS.changeMe {
    background-color:yellow;
    width:200px;
}
```

```javascript
{
    applyCSS: function(cmp, event) {
        var cmpTarget = cmp.find('changeIt');
        $A.util.addClass(cmpTarget, 'changeMe');
    },
    
    removeCSS: function(cmp, event) {
        var cmpTarget = cmp.find('changeIt');
        $A.util.removeClass(cmpTarget, 'changeMe');
    }
}
```

* In Aura framework arbitrary JavaScript, such as the `alert()` call, in the component is ignored.
* The framework has its own event system. DOM events are mapped to Lightning events, since HTML tags are mapped to Lightning components.
* Any browser DOM element event starting with on, such as onclick or onkeypress, can be wired to a controller action.
* You can only wire browser events to controller actions.
* The "Framework" button wires the onclick attribute in the `<lightning:button>` component to the handleClick action in the controller.

```xml
<aura:component>
    <aura:attribute name="text" type="String" default="Just a string. Waiting for change."/>
    <input type="button" value="Flawed HTML Button"
        onclick="alert('this will not work')"/>
    <br/>
    <lightning:button label="Framework Button" onclick="{!c.handleClick}"/>
    <br/>
    {!v.text}
</aura:component>
```

```javascript
({
    handleClick : function(cmp, event) {
        var attributeValue = cmp.get("v.text");
        console.log("current text: " + attributeValue);

        var target = event.getSource();
        cmp.set("v.text", target.get("v.label"));
    }
})
```
* The handleClick action uses `event.getSource()` to get the source component that fired this component event.
* In this case, the source component is the `<lightning:button>` in the markup.
* The code then sets the value of the text component attribute to the value of the button’s label attribute.
* Handle framework events using actions in client-side component controllers. 
* Framework events for common mouse and keyboard interactions are available with out-of-the-box components.

### Accessing Component Attributes
In the `handleClick` function, the first argument to every action is the component to which the controller belongs.
*  look at and change its attribute values
    * `cmp.get("v.attributeName")` returns the value of the attributeName attribute
    * `cmp.set("v.attributeName", "attribute value")` sets the value of the attributeName attribute

### Invoking Another Action in the Controller

