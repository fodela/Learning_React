## Notes:

1.  We name global variable in all uppercase separated by underscores

## Setting up a react Project

1. ## Create a React app

```
npx create-react-app app-name

//For the lesson purposes use:

npx create-react-app app-name --scripts-version 1.1.5
```

app-name is case sensitive and should be all lowercase.

2. ## Enable css module

- css module allow us to reuse css classes and prevent the from being applied globally (affecting every element.)

```
//In terminal;
cd app-name

npm run eject

//In config/webpack.config.dev.js find where we have 'test: \/.css$/, in options={
   importLoader: 1,
   // copy and paste the above into the second webpack file's option:

   //add
   modules:true,
   localIdentName: '[name]__[local]__[hash:base64.5]'
}

// '[name]_[local]_[hash:base64:5]' => [original name of class]_[local name of component]_[added to a hash named base64:5]
```

3. Clean up the app by
   1. deleting the svg file and its import
   2. clear the App.js content and remove the class from the div
   3. Delete the App.css file
   4. Redefine your global reset in index.css and also add fonts to index.html

## NB:

      - React must be imported in every file that renders a JSX code.

## How to create a component

Two options

1. class based

   ```
    class ComponentName extends Component {
    render(){
    return(
    <div some elements /div>
    )
    }
    }
   ```

2. function based

   ```
   import React from 'react';

   //for components that need js logic or functionalities before outputting or returning Jsx

   const componentName = ()=>{
   return(
   <div some elements /div>
   )
   }

   //for components that only return jsx
   const componentName = ()=>(
      <div>some elements<div>
   )

   export default componentName
   ```

## How to pass down a function

1. add the listener to the component by a ref name

```
      <Component onClick={props.clicked}>

```

2. assign function to ref name where the component is rendered

```
      <ComponentRenderer>
         <Component clicked={this.functionNameHandler}
      </ComponentRenderer>
```

3. Create the function

```
const functionNameHandler => {
dosomething...
}
```

## conditional rendering

1.  def a state to be false
    state = {
    showInDom:false
    }
2.  def function that will change state
    const showInDomHandler = () => {
    doesShow = !this.state.showInDom
    this.setState({showInDom : doesShow})
    }

3.  change the state base on condition
    if(showInDom){
    <!-- def element to show -->
    navigation = (
    <div
    contents
    /div>
    )
    }
4.  Display element in render(){
    return(
    <div {navigation}
    /div>
    )
    }

## Custom Error Handling

- Create error boundary Component

## Deleting specific components

- Get the index or key of the component you want to delete
- Create a copy of the list in which the component is -- so as not to modify the original list directly. Is a good practice to prevent unforeseen complications.
- Splice off that component out of the list
- Update the state

## Change specific component's information

1. get component whose info you want to change by writing a conditional that compare all element in the list of components to the the id you passed in the function.
2. Because js object are just a pointer of the original object we must replicate the object before modifying it.

```
Object.assign({},this.state.persons[personIndex])
```

or the spread operator

```
const person = {...this.state.persons[personIndex]}
```

```
const nameChangeHandler = (event, id) => {
      // get index of the desired element in the array of component
      const personIndex = this.state.persons.findIndex((p) => {
        return p.id === id;
      });

      // Get replica of person
      const person = { ...this.state.persons[personIndex] };
      // change person info
      person.name = event.target.value;
      // replicate persons
      const persons = [...this.state.persons];
      // insert modified person into persons
      persons[personIndex] = person;

      this.setState({ persons: persons });
    };
```

## A Better work structure

- containers that manage state should not have too much ui rendering or presentation
- class base component for state management and function base for presentation component

#### Stateless vs Stateful

- Majority of your component should be damn or stateless components this way is easier to manage as your projects grow
- Stateless component are kept in the components folder while stateful ones are kept or created in the containers folder

#### Class based vs function based

- In older version (without hooks):
  - Use class base for stateful and function based for stateless
    -Newer version (>= v-16)
- Use class base to manage state or access lifecycle hooks and function based to access state but not lifecycle hooks

##### NB

- class based uses this.state and this.props

### How to:

1. Create a component
2. import the components you need
3. refer to functions you need that are not defined here and normal js method with with "props."
4. Export your component
5. Import in the component you want to use it in

### Component lifecycle

1. Component creation lifecycle

   - Only in class based components but has an equivalent in functional components when using react hooks
     NB: lifecycle hooks are not react hooks.

   - Component lifecycle - Creation

   - There are those that run whenever something change and those that run when the component is rendered for the first time.
   - Constructor(props) => getDerivedStateFromProps(props,state) => render() => Render Child Components => componentDidMount()

   1. Constructor(props):
      Default ES6 class
      Call super(props) if you want to input your own props.

      - Use: to set initial state
      - Don't Use: to cause side Effects (send Http Request, store something in local storage, send analytics to google analytics)

   2. getDerivedStateFromProps(props,state)
      version 16.3 and above

      - Use: to sync state (whenever props change for your class-based component, you can sink your state to them.) Rarely used
      - Don't use: Cause to side Effect

   3. Render():

      - Use:to render (use to prepare the data as you need it to lay out your Jsx)
      - Don't use: HTTP request or set any timeouts so that nothing block the rendering process.

   4. Render Child Components:
      All children in render will be rendered

   5. componentDidMount():
      Very important
      - Use: to cause side effect
      - Don't use: to update state unless let's say the then block of a promise after you sent an HTTP request. Else it will trigger a re-render cycle and that's bad for the component.

2. ### Component Update lifecycle for props changes

   - getDerivedStateFromProps(props,state) => shouldComponentUpdate(nextProps, nextState) => render() => Update child Component Props => getSnapshotBeforeUpdate(prevProps, prevState) => componentDidUpdate()

   - getDerivedStateFromProps(props,state)
     -update state base on external properties or changes. We will rarely use this
     -use: to sync state to props
     -Don't: Cause Side Effects

   - shouldComponentUpdate(nextProps, nextState)

     - Allow to cancel the updating process
     - Use: To decide whether to continue or not (for optimization purposes)
     - Don't: cause side effects

   - getSnapshotBeforeUpdate(prevProps, prevState)
     - Before the update happen, a snapshot was taking to be able to restore unaffected components(such as getting the current scrolling position of the user). This method get the snapshot and restore the DOM
     - Use: For last minute DOM operation
     - Don't Use: Cause Side Effects

- componentDidUpdate()
  - Signal that update is done
  - Use: Do cause side effect but be careful not enter infinite loop
  - Don't Use: Update state (triggers re-render cycle)

3. Using useEffect lifecycle Hook for functional components

   - Second most important react hook next to useState
   - Nowadays it is possible to code everything using function based components using the useEffect.
   - useEffect captures all the class based lifecycle hooks in one React hook
   - Though they are both called hook useEffect is not a lifecycle but rather a react hook, so basically a function you can add into one of your functional components.
   - It runs with every update and render cycle. Thus it combines componentDidMount and componentDidUpdate in one effect.
   - How it works

     - Can be added anywhere in the functional component and it takes a function that will runs with every render cycle.

   - useEffect run with every cycle that could be problematic how do we fix that? By controlling when it execute.

     - The first argument of useEffect is the function it runs every time.
     - We can add a second argument that when changed then useEffect should execute.
     - If we want it to run only the first time when the component is rendered then we just pass in an empty argument.
     - arguments are passed as

     ```
     [props.argumentName1,props.argName2]

     useEffect(()=>{
     do something...
     },[props.componentName])

     ```

4. Cleaning up with lifecycle Hook

- Clean up is when you want to remove anything that was previously set. E.g removing an event listener after a requirement is met.

  1.  componentWillUnmount() => This executes when a component is removed. We can insert a function that executes right before the component is removed.

  2.  In function based components
      we return a function inside the function that useEffect takes as it first arg. This function just like in componentWillUnmount runs only when a component is unmount. Just like useEffect we can manage when it runs by adding a second arg in useEffect.
      We can code a functionality that will control how things happen in the function we return in useEffect.

5. Using shouldComponentUpdate for optimization.
   Without this react re-renders every child component as long as the parent state is change. To fix this:
   we use shouldComponentUpdate to check if the next state of the component = the current state of the component. if not then component should update thus re-rendered.
   This only works in our example because we recreated a copy of the persons array when we used the spread operator. Since array are reference types we need to actually create a copy before the pointer can change.

6. How to optimize functional components
   shouldComponentUpdate is only available in class based component. The substitute by wrapping the whole functional export with React.memo()
   This memorizes the state of the component and only re-rendered when the state change.

7. When should you optimize?
   When parent change but in a way that does not affect this component at all.
   Not used when the component has to update anytime the parent updates. The optimization check is unnecessary and only slows down the application.

8. pureComponent vs shouldComponentUpdate
   pureComponent is a normal component that already implement shouldComponentUpdate with complete props check
   It is used when a number of factors have to be checked using shouldComponentUpdate in order to optimize the application
   It is imported and extended in class based component instead of the normal component.

   ```
   import {pureComponent} from 'react

   class myComponent extends pureComponent(){

   }
   ```

9. How does React update the real DOM?
   the method render() does not automatically update the DOM
   - React uses two virtual DOMs
     1. an old virtual DOM and a
     2. Re rendered virtual DOM => is created using render()
   - React then compares the two DOMs and only update the difference and not re-render the whole DOM. Because accessing the DOM is very slow and functionality expensive.
   - shouldComponentUpdate allows us to manipulate when a sub-component should update when its parent is updated.

### Rendering Adjacent JSX Element

- So far React only allows us to render a single child (usually '<div>')

1. We return an array instead with each element having a unique key and separated by ','
2. A better way is to create a higher order component.
   Inside, create Auxillary.js file
   ```
   const aux = props => props.children;
   export default aux
   ```
3. We can use the REact 16.2 in-built aux component called Fragment
   ```
   import {Component, Fragment} from 'react'
   <Fragment>
   children
   </Fragment>
   ```

### Higher Order Component

- They are components that wrap other components and add something (e.g styling error handling,logics etc) to it
- they, by convention, start with 'With'
- 2 ways to create it

  1.  Using props => Used for changing style handle error etc...

      ```
      const WithClass = (props)=>(
      <div className = props.classes>
      props.children
      </div>
      )
      )
      //How it is used
      //We import and wrap our component with it
      <WithClass classes='AppStyle>
      <OurComponent/>
      <WithClass>
      ```

  2.  Return a js function => Used for logic
      Name start with lowercase
      ```
      const withClass = (WrappedComponent,className) => {
      return props => (
      <div className = {className}>
      <WrappedComponent>
      </div>
      )
      }
      //How it used
      //Wrap the entire component with it
      export default withClass(App, myClassName)
      ```

### Passing unknown props

- While working with hoc we cannot pass props. This challenge may be overcome by passing prop to the wrapped component using the spread operator
  ```
  <WrappedComponent {...props}/>
  ```

### Setting State Correctly

When we are doing state update that depends on the old state, we don't just set the new state directly, instead we pass in a function that return stateName : prevState.stateName + changes

```
this.setState((prevState,props)=>{
return {
this.stateName: prevState.stateName + changes
}
})
```

### Using PropTypes to improve how we receive props

- Is used to set which prop our component uses and pass an error when an incorrect prop is passed.
- This is only needed if you are building a library that other will use or working in a bigger team.
- With this if anyone pass the incorrect type of props they get a warning.
- How it is done

  ```
  //install prop-types
  npm install --save prop-types

  //import it into your component
  import PropTypes from 'prop-types'

  ComponentName.propTypes = {
  propName: PropTypes.func,
  propName: PropTypes.string
  }
  ```

### Using Refs

The 'ref' keyword can be used in to reference a particular element we want to select. It is like adding an id to allow selection using 'document.getquerySeletor' of vanilla js.

NB: references can also be used to store value but is an advanced concept for later.

- How it used:
  Say we want to focus a specific element

1.  ```
    // set it up
    <element
    ref={(elementAlias)=>{this.htmlElement = elementAlias}}
    />

    //We use it the way we want
    componentDidMount(){
    this.htmlElement.focus()
    }
    ```

2.  React 16.3 and above

    ```
    constructor(props){
    super(props);
    elmentAlias = React.createRef();
    }
    <element
    ref={this.elementAlias}
    />

    componentDidMount(){
    this.elementAlias.current.focus()
    }
    ```

### Using Refs with React Hooks

Refs can also be used for function based components

```
import {useEffect, useRef} from 'react'
// first set it to null
const specificBtnRef = useRef(null)
// inside useEffect (this will allow the DOM to be be rendered first) implement what you want.
useEffect(()=>{
specificBtnRef.current.click()
},[])
```

### Understanding Prop Chain Problems

- For a component A with subcomponent B which has another subcomponent C then subcomponent D, to pass a prop from A to d we have to pass the prop through B and C. This is a problem. We can solve it using context.

### Using context API

It is a js object (not just dictionary) that can be passed between component without using props.

- It takes the variable to the subcomponent without having to pass through every single supercomponent before reaching the targeted subcomponent.

- How it is used

  1.  the context component

      ```
      import React from 'react'
      const authContext - React.createContext({authenticated: false,login:()=>{}})

      export authContext

      ```

  2.  In our component A from the above e.g. Wrap ur component with the context component after importing the context component.Provider as a child

  3.  define the value in the wrap

      ```
      import AuthContext from ../context/auth_context
      <AuthContext.Provider
      value={{
      authenticated:this.state.authenticated,login:this.loginHandler}
      }>
      <myComponent/>
      </AuthContext.Provider>
      ```

  4.  In our component D from the above e.g,
      wrap what ever I want the variable for as a dynamic variable and a child of AuthContext.Consumer
      ```
      <AuthContext.Consumer>
      {(context)=> the way I want to use the variable}
      </AuthContext.Consumer>
      ```

NB: this however does not allow us to access the variable (context) anywhere else in the class or function. We can solve this using: contextType

### Using contextType

- Is a better way to use context in class based component

- React 16.6 and above introduced contextType
- How it is used

  ```
  const contextType = AuthContext

      // Where you want to use it
      {this.context.authenticated}

  ```

### Using useContext

- A better way to use context in functional components.

  ```
  import { useContext } from 'react'

  static authContext = useContext(AuthContext)

  // At where you want to use it
  <button onClick={authContext.login}>
  '''
  ```

## How to plan a react Application / Project

1. Component Tree / Component Structure
   Designed ui => components tree

2. Application State (Data)
   The states or data you are going to use in the application

3. Components vs Containers
   stateless vs stateful

### Adding more than one css class to an element

- css classes are string type so we pass them as an array and then join them with " "

```
className={[classes.Button, classes[props.btnType]].join(" ")}

```

### Working with Images

We need to make webpack aware of us using images. We do this by importing the image to the file in which we want to use it:

```
import image from "./imageFolder/image.png"
```

# Reaching out to the Web

### Http Requests in React (Typically)

Unlike the normal webpage where the browser send a request and gets back an html page, react gets back json data when it send a request.

In the lesson, we used json placeholder

### axios

Is js library that handle http request

```
   //axios Installation
   npm install axios --save
```

### Sending an Http request or get data

- ComponentDidMount is the best place to send the request and cause side effects
  get request is asynchronous and cannot be saved into a constant.
  axios.get return a promise that we can use .then on to save the request in a constant
- Anything you want to do with the response should be done in the .then and not after because the after line don't wait for the async to finish executing before they do.

NB: when we update the state in componentDidUpdate we creates an infinite loop.

```
componentDidMount(){
   axios.get('https://jsonplaceholder.typicode.com/posts').then(response=>{
      console.log(response)
   }
   )
}
```

### How to post data

axios.post(address, dataToBePosted)

```
axios.post('https://jsonplaceholder.typicode.com/posts')
   .then(response => {
      //doSomething...
   })
```

### How to delete data

```
   axios.delete('https://jsonplaceholder.typicode.com/posts/' + this.props.id)
      .then(response =>{
         //dosomething...
      })
```

### How to handle errors locally

```
   axios.get(url)
      .then()
      .catch(error=>{log or show the error})
```

### How to execute code globally using interceptors

Interceptors are functions that execute globally for every request sent or every response obtained

- Uses:
  - used for setting some common headers like authorization header
  - used to log responses
  - handle errors globally

```
// In index.js
// For sending request
axios.interceptors.request.use(request => {
   //dosomething...
   return request
}, error =>{
   //handle the error
   return Promise.reject(error);
})

//For getting response
axios.interceptors.response.use(
   response => {
      // dosomething
      return response
   },
   error => {
      //handle error
      return Promise.reject(error)
   }
)
```

### Setting a default global configuration

For example if the server url is always the same we can set a default global config that whenever we type the rest into the get or post request it knows exactly the link.

```
//In index.js (is the first file to be executed)
axios.defaults.baseURL ="https://url.com"

axios.defaults.headers.common["Authorization"] = "AUTH TOKEN";
axios.defaults.headers.post["Content-type"] = "application/json";
```

### Creating and using instances using axios

If the url is not the same for all request and post we use instances

## Routing (Multiple Pages in a Single Page Application)

It uses javascript to render different part of a single pages based on different path.

The router package parse the url/path => read config => render / load appropriate JSX/Component

NB: Not inbuilt in react, but rather a third party library

We use two packages and install them the same way as before 1. react-router => contains the logic 2. react-router-dom => enable rendering to the dom

### How to enable routing

Is done in App.js or Index.js

```
   import { BrowserRouter } from 'react-router-dom'

   <BrowserRouter>
      <Component />
   </BrowserRouter>
```

### Route | Rendering specific pages

````
<BrowserRouter>
   <Routes>
      <Route path="/" exact render={()=> <h1>Home</h1>}>\
   </Routes>
</BrowserRouter>
```
path="/" => is my path starting with "/"
exact => is my path exactly "/"
render={()=>JSX} => takes a functional component

We can next multiple route into the same page.
````

### How to prevent page from reloading when we click on a link.

```
import { Link } from 'react-router-dom'
```

Replace
`<a href="path_or_url">MyLink</a>`
with

```
<Link to="path_or_url">MyLink</Link>
```

### How to access Route props

We wrap the component with withRouter

```
import withRouter from 'react-router-dom'
export default withRouter(post);
```

### Creating a relative path

- This attaches the new path to the end of the current path

```
<Link to={pathname: this.props.match.url + "path_or_url"}>MyLink</Link>
```

### Creating an active link

Use NavLink instead of Link => this appends an "active" class to the clicked link which we can style differently in our css file.
Remember to add exact

```
<NavLink to="/" exact>Home </NavLink>
```

We can also customize the active class by

```
<NavLink
activeClassName="my-active"
activeStyle={{color: 'green',
               textDecoration: 'underline'}}
>Home </Navlink>
```

### How to get parameters of a clicked element

```
// example id
componentDidMount(){
   cardId = this.props.match.params.id
}
```
