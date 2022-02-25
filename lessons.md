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
   const ComponentName = ()=>{
   return(
   <div some elements /div>
   )
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
- Create a copy of the list in which the component is -- so as not to modify the original list directly. Is a good practice to prevent unforseen complications.
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

     '''
     [props.argumentName1,props.argName2]

     useEffect(()=>{
     do something...
     },[props.componentName])

     '''

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
   '''
   import {pureComponent} from 'react

   class myComponent extends pureComponent(){

   }
   '''

9. How does React update the real DOM?
   the method render() does not automatically update the DOM
   - React uses two virtual DOMs
     1. an old virtual DOM and a
     2. Re rendered virtual DOM => is created using render()
   - React then compares the two DOMs and only update the difference and not re-render the whole DOM. Because accessing the DOM is very slow and functionality expensive.
   - shouldComponentUpdate allows us to manipulate when a sub-component should update when its parent is updated.

### Rendering Adjascent JSX Element

- So far React only allows us to render a single child (usually '<div>')

1. We return an array instead with each element having a unique key and separated by ','
2. A better way is to create a higher order component.
   Inside, create Auxillary.js file
   '''
   import React from 'react';
   const aux = props => props.children;
   export default aux
   '''