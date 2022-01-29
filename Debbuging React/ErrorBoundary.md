# Custom Error Handling using Error boundary

ErrorBoundary is a higher-order component that handles error in a way we want

## How to:

- ### Create error boundary Component

  - #### Create error boundary class

    1. Declare your error variable (example hasError) and error message variable (what you want to output) in state
    2. change state if an error occurs using the componentDidCatch method.
    3. Render and return JSX containing based on your conditions.
    4. Export ErrorBoundary.

       ```
       import React, { Component } from "react";

       class ErrorBoundary extends Component {
           state = {
           hasError: false,
           errorMessage: "",
           };

           componentDidCatch = (error, info) => {
           // is a method by react that execute when an error occur in your code
           // change state to have an error since this only fires when there is an error.
           this.setState({ hasError: true, errorMessage: error });
           };

               render() {
                   if (this.state.hasError) {
                       return (
                           <div>
                           <h2>Something went wrong</h2>
                           <p>{this.state.errorMessage}</p>
                           </div>
                       );
                   } else {
                   return this.props.children;
                   }
               }
           }
       export default ErrorBoundary;
       ```

    5. Import ErrorBoundary in your component and
    6. Wrap your component or app with ErrorBoundary

       - Remember to include any key in ErrorBoundary since it is now the outermost component.

       ````
        import ErrorBoundary from "./ErrorBoundary/ErrorBoundary"

        return (
              <ErrorBoundary key={person.id}>
                <Person
                  name={person.name}
                  age={person.age}
                  changeName={nameChangeHandler}
                  click={() => deleteDetailHandler(index)}
                />
              </ErrorBoundary>
            );
       ```[]
       ````
