# React Unit Testing

This tutorial shall help setting up Unit Tests for your React Application.

We will do Unit Testing on React Components and the Redux State within these 2 Stacks:

- using Mocha and Chai (our own Stack)
- using Jest (Create React App Starter Kit) plus Enzyme

*Notes:*
- Unit Tests on React Components should focus on Interactions (example: user clicks a button, or presses a key: then test if a function gets called with the right arguments) with React Components, NOT too much on the Structure of React Components. And surely NOT on the business logic of React Components as business logic should be pulled out of your React Components.
- Jest Snapshots: Save time on Testing on React Components' Outputs (Structure, NOT Interactions) through Snapshots (`npm install --save-dev react-test-renderer`)
    - https://reactjs.org/docs/test-renderer.html
    - https://github.com/facebook/react/tree/master/packages/react-test-renderer
    
    Jest will "complain" if the snapshot differs from previous test, so you will be reminded to doublecheck the differences and, if you did a change on purpose, you can run jest with the -u flag (to update the previous shapshot with the actual one).


## Table of Contents

1. [Getting started](#chapter1)
2. [Two Test Stacks](#chapter2)
    1. [Unit Testing with Stack `Webpack`, `Babel`, `Mocha`, `Chai`](#chapter2a)
        1. [Unit Testing React Components](#chapter2a1)
        2. [Unit Testing the Redux State](#chapter2a2)
    2. [Unit Testing with `Create React App` Starter Kit (Integrated: `Webpack`, `Babel`, `Jest`) plus `Enzyme`](#chapter2b)
        1. [Unit Testing React Components](#chapter2b1)
        2. [Unit Testing the Redux State](#chapter2b2)
3. [Links](#chapter3)


## <a id="chapter1"></a>1. Getting started

### The usual order of writing tests BEFORE delivering a new software or software version to the client:

1. Unit Tests:
    - A single piece of code (usually a function or a class/object) is tested, separate from other pieces.
    - Unit Tests are very helpful if you need to change your code: If your set of Unit Tests verify that your code works, you can safely change your code and have confidence that the other parts of your program will still work as expected.
    - Unit Tests wording can be used for documentation.
    - Save time because you don't have to do manual testing after working on your code again and again. And again.

    TDD: Unit Testing brings us to the terminology TDD (Test-Driven Development / or Design):
    - Some quotes:
        - "Keep it simple, stupid!" (KISS) 
        - "You aren't gonna need it!" (YAGNI)
    - You can better focus on a demanded requirement by writing very specific test cases first. This way you are forced to work and concentrate on the demanded requirement only, NOT on functionality that is NOT proven to meet the requirement or was never demanded by the client (see MVP: https://en.wikipedia.org/wiki/Minimum_viable_product).
    - By focusing on writing only the code necessary to pass the tests, designs can often be cleaner and clearer than it is achieved by other methods.
    - TDD Cycle:
        1. Add a test
        2. Run all tests and new test will fail as code not written yet: This way we check if the new test really works
        3. Write the code
        4. Run tests: If all test cases pass, go to next step
        5. Refactor code: Clean up
    - Critics:
        - The difficult thing about TDD for many developers is that you have to write tests BEFORE writing any code.
        - If you're the client and the developer in one person, and you're not quite sure yet how your software should convince and appear to users, better focus on options, brainstorming, and branding than on writing test cases. 

    BDD: TDD brings us to the terminology BDD (Behaviour-Driven Development / or Design):
    - Sets focus on User Stories and their scenarios/events.
    - Sets more focus on the wording for better understanding and documentation.
    - Therefore makes it easier to show test results to the business side to make the client happy - and yourself.

2. Integration Tests: 
    - Pieces of code (usually organized in components and Services/APIs) are tested as a group among themselves or tested against Third-Party Software (like a Database or Push Notification Service).
    - To simulate user actions in a browser, we will use jsdom module, which is a great DOM implementation in Node.js and gives us a "fake" DOM entirely in JavaScript to run our automated tests against.
    - Examples: 
        - Testing if a login component running on a browser connects to a Service/API running on a server successfully.
        - Testing if a Service/API returns data from a database successfully.
        - Testing if a message component running in a progressive web app returns a success message from the database after writing to it.

3. End-to-end Tests (E2E):
    - End-to-end Test make sure that all components of an application and all Third-Party Software work together as expected. 
    - The application should be tested with production data under real-time (stress) conditions against the test setup in order to do check performance as well. 
    - The name End-to-end means that the communication and data transfer of the application works as expected from the one end (the client interface) throughout the other end (the database or another client interface) and vice versa.
    - Also called System Test

### When all tests above are successful:
 
4. User Acceptance Tests:
    - The Client/Purchaser/Sponsor is involved.
    - Many manual / decision making steps.
    - Questions:
        - Are all requirements of a specification or contract met?
        - Is performance good enough?
        - Did QA Department test edge cases manually?
        - Did QA Department test security manually?
        - Is Software/New Version ready to be released/integrated into IT landscape?
        - Regression Tests successfully after integrating New Version into IT landscape? Do all previous features still work as expected?


Ok, now let's dive into Unit Testing using BDD.

## <a id="chapter2"></a>2. Two Test Stacks

We want to show Unit Testing along these two BDD frameworks, but with the same outcome:

1. Mocha/Chai

2. Jest/Enzyme 

It will of course depend on your stack and your preference which framework to use.
 
*Note:*
- For a list of other Unit Test JavaScript frameworks, see https://en.wikipedia.org/wiki/List_of_unit_testing_frameworks#JavaScript
- Of course you are not bound to a JavaScript framework.

## <a id="chapter2a"></a>i. Unit Testing with Stack `Webpack`, `Babel`, `Mocha`, `Chai`

### A Note to Mocha/Chai:

Mocha is the container in which Chai Code is running.

### Preparation

First install all dependencies:

```
> cd unit-testing-mocha-chai
> npm install
```

When done, run the tests:

```
> npm run test
```

The outcome will be:

```
> npm run test

> testing-with-webpack-and-chai-mocha@1.0.0 test /Users/piano/Desktop/Projects/react-testing/unit-testing-mocha-chai
> mocha --compilers js:babel-core/register --require ./test/test_helper.js --recursive ./test

  actions
    saveComment
      ✓ has the correct type
      ✓ has the correct payload

  App
    ✓ shows comment box
    ✓ shows comment list

  CommentBox
    ✓ has the correct class
    ✓ has a text area
    ✓ has a button
    entering some text
      ✓ shows that text in the textarea
      ✓ when submitted, clears the input

  CommentList
    ✓ shows an LI for each comment
    ✓ shows each comment that is provided

  Comments Reducer
    ✓ handles action with unknown type
    ✓ SAVE_COMMENT


  13 passing (943ms)

> 
```

To add a watcher to always re-run all tests after a change in code:

```
> npm run test:watch
```

### <a id="chapter2a1"></a>a. Unit Testing React Components

As example we will dive into the Unit Test of the App Component and play with it.

Let's explore `test/components/app_test.js`:

```
import { renderComponent, expect } from '../test_helper';
import App from '../../src/components/app';

// Use 'describe' to group together similar tests
describe('App', () => {

    let component;

    // First create an instance of App
    beforeEach(() => {
        component = renderComponent(App);
    });

    // Use 'it' to test a single attribute of a target
    it('shows comment box', () => {

        // Use 'expect' to make an 'assertion' about a target
        expect(component.find('.comment-box')).to.exist;
    });

    it('shows comment list', () =>{
        expect(component.find('.comment-list')).to.exist;
    });
});
```

1. First we import the test_helper.js file to enable Mocha/Chai.
2. We need to also import `app.js` as well, but we leave it WITHOUT CommentList and CommentBox Components ...
 
    ```
    <CommentBox />
    <CommentList />
    ```
                     
    ... because we want our test to FAIL initially !
    
    ```
    import React, {Component} from 'react';
    
    class App extends Component {
        render() {
            return (
                <div className="App">
                    <header className="App-header">
                        <img src="/logo.svg" className="App-logo" alt="logo"/>
                        <h1 className="App-title">Welcome to React</h1>
                    </header>
                    <div className="Test-container">
                    </div>
                </div>
            );
        }
    }
    
    export default App;
    ```
3. Description of Chai commands:

    - The `describe('App', () => {});` command groups together different tests within the same context, for better readability.
    - The `beforeEach(() => {})` command is called before EACH `it('attribute to test', () => {});` command is executed.
    - The `it('shows comment box', () => {});` command groups together the final Unit Test(s).
    - The `expect(component.find('.comment-box')).to.exist;` command executes the final Unit Test, testing our assertion (assertion) that, in this case, a CommentBox exists within our App Component. 
    - Check out http://chaijs.com/api/bdd for many many things you can test !


4. Next, we run the test, and the outcome will be:
    
    ```
    ...
    
    11 passing (841ms)
    2 failing
    
    1) App
         shows comment box:
       AssertionError: expected undefined to exist
        at Context.<anonymous> (test/components/app_test.js:18:9)
    
    2) App
         shows comment list:
       AssertionError: expected undefined to exist
        at Context.<anonymous> (test/components/app_test.js:22:9)
         
    npm ERR! code ELIFECYCLE
    npm ERR! errno 2
    npm ERR! testing-with-webpack-and-chai-mocha@1.0.0 test: `mocha --compilers js:babel-core/register --require ./test/test_helper.js --recursive ./test`
    npm ERR! Exit status 2
    npm ERR! 
    npm ERR! Failed at the testing-with-webpack-and-chai-mocha@1.0.0 test script.
    npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
    
    npm ERR! A complete log of this run can be found in:
    npm ERR!     /Users/piano/.npm/_logs/2018-06-30T14_47_30_101Z-debug.log
    >
    ```
 
5. Put back CommentList and CommentBox Components in `app.js` and all tests will again pass.

### <a id="chapter2a2"></a>b. Unit Testing the Redux State

As second example we'll check if our Reducer `src/reducers/comments.js` works correctly. 
We do it through test file `test/reducers/comments_test.js`:

Here are 2 things we want to test:

- Will reducer return an empty array as new state (an empty array to not to break anything) if we pass it a state of undefined ?
- Will action-payload of 'new comment' really return 'new comment' as new state ?

```
describe('Comments Reducer', () => {

    // in case there is a weird input, we react with the default (initial) state
    it('handles action with unknown type', () => {
    
        expect(commentReducer(undefined, {})).to.eql([]);
    });

    it('SAVE_COMMENT', () => {

        const action = { type: SAVE_COMMENT, payload: 'new comment'};
        expect(commentReducer([], action)).to.eql(['new comment']);
    });

});
```

## <a id="chapter2b"></a>ii. Unit Testing with `Create React App` Starter Kit plus `Enzyme`

First install all dependencies:

```
> cd unit-testing-jest-enzyme
> npm install
```

*Note:*
`npm install` will install the `Create React App` Starter Kit plus:

- enzyme
- enzyme-adapter-react-16
- redux-mock-store

`enzyme` and `enzyme-adapter-react-16` are modules of AirBnb and recommended by Facebook. They are needed to easier mount your React Components, also enabling to connect your Redux store.

`redux-mock-store` will be installed to give you the option to use a mock Redux store, independent of your own Redux store. This may make sense in some scenarios. 

When done, run the tests:

```
> npm run test
```

The outcome will be:

```
> npm run test
 PASS  src/App.test.js
 PASS  src/components/comment_box.test.js
 PASS  src/components/comment_list.test.js
 PASS  src/actions/index.test.js
 PASS  src/reducers/comments.test.js

Test Suites: 5 passed, 5 total
Tests:       13 passed, 13 total
Snapshots:   0 total
Time:        1.732s, estimated 3s
Ran all test suites.

Watch Usage: Press w to show more.

```

### <a id="chapter2b1"></a>a. Unit Testing React Components

As example we will show again the Unit Test of the App Component: `src/App.test.js`

```
import React from 'react';
import App from './App';
import { mount } from 'enzyme';

// We need to wrap CommentBox with <Provider> tag in first beforeEach(() => {}) below;
// otherwise we receive this error message:
// Invariant Violation: Could not find “store” in either the context or props of “Connect(CommentBox)”
// https://stackoverflow.com/questions/36211739/invariant-violation-could-not-find-store-in-either-the-context-or-props-of-c
// Also see comment_list.test.js
import {configure} from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';
import {Provider} from "react-redux";
import {createStore, applyMiddleware} from 'redux';
import reducers from './reducers';

configure({adapter: new Adapter()});
const createStoreWithMiddleware = applyMiddleware()(createStore);

// Use 'describe' to group together similar tests
describe('App', () => {

    let component;

    beforeEach(() => {
        component = mount(<Provider store={createStoreWithMiddleware(reducers)}><App /></Provider>);
    });

    // Use 'test' or it' (both possible) to test a single attribute of a target
    test('shows comment box', () => {

        expect(component.find('.comment-box').length).toBe(1);
    });

    test('shows comment list', () => {
        expect(component.find('.comment-list').length).toBe(1);
    });
});
```

### <a id="chapter2b2"></a>b. Unit Testing the Redux State

As second example again we'll check if our Reducer `src/reducers/comments.js` works correctly. 
We do it through test file `src/reducers/comments.test.js`:


```
import commentReducer from './comments';
import { SAVE_COMMENT } from '../actions/types';

describe('Comments Reducer', () => {

    // in case there is a weird input, we react with the default state
    test('handles action with unknown type', () => {

        expect(commentReducer(undefined, {})).toEqual([]);
    });

    test('SAVE_COMMENT', () => {

        const action = { type: SAVE_COMMENT, payload: 'new comment'};
        expect(commentReducer([], action)).toEqual(['new comment']);

    });

});
```

## <a id="chapter3"></a>3. Links

### Have a look !

Testing with React-Redux App: 
- Stephen Grider, Repo: https://github.com/StephenGrider/AdvancedReduxCode
- Stephen Grider, Udemy Course "Advanced React and Redux": https://www.udemy.com/react-redux-tutorial

Mocha/Chai:
- https://github.com/chaijs/chai-jquery
- http://chaijs.com/api/bdd

Jest/Enzyme:
- Jest: 
    - https://github.com/facebook/jest
    - http://jestjs.io/docs/en/expect.html#content
- React Test Renderer (Jest Snapshot): 
    - https://reactjs.org/docs/test-renderer.html
    - https://github.com/facebook/react/tree/master/packages/react-test-renderer
- Enzyme: https://airbnb.io/enzyme/docs/api/
- Manuals:
    - https://www.sitepoint.com/test-react-components-jest/
    - https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests

Unit Tests:
- https://en.wikipedia.org/wiki/Unit_testing
- https://en.wikipedia.org/wiki/List_of_unit_testing_frameworks#JavaScript
- https://hackernoon.com/testing-your-frontend-code-part-ii-unit-testing-1d05f8d50859
- https://codeutopia.net/blog/2015/03/01/unit-testing-tdd-and-bdd/
- https://en.wikipedia.org/wiki/Behavior-driven_development
- https://en.wikipedia.org/wiki/Test-driven_development

Integration Tests:
- https://hackernoon.com/testing-your-frontend-code-part-iv-integration-testing-f1f4609dc4d9
- https://github.com/jsdom/jsdom

End-2-end Tests:
- https://hackernoon.com/testing-your-frontend-code-part-iii-e2e-testing-e9261b56475
- https://www.techopedia.com/definition/7035/end-to-end-test
- https://medium.freecodecamp.org/why-end-to-end-testing-is-important-for-your-team-cb7eb0ec1504

Acceptance Tests:
- https://en.wikipedia.org/wiki/Acceptance_testing


### Credits to the authors of above links ! Thank you very much !

### And credits to the reader: Thanks for your visit !