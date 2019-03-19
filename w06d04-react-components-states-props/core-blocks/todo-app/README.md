# TODO app using React

## Release 0

Create a TODO app using react such that the app should contain following features: 

- whenever the user adds the to-do list item it should display below
- create a close button at the end of each list item such that if you click it the event should be deleted from the list
- if we refresh the page, the list should should remain same (use localStorage to store to-do list)

## Release 1

Create a TO-DO form, which is a "Presentation component", to add TO-DO item in the list whenever we submit the form

Create a TO-DO list component which present the list of TO-DO. Remember to add the remember remove property which is an event handler that will be called when the list item is clicked.

Now we have a form and the list which are independent of each other but we need to do some tying together where needed. Create a "Container Component", which is the heart of this application, by regulating props and managing state among the presentation components.

Finally render our components to the browser using `ReactDOM.render()` method.

## Release 2 

Style the TO-DO app. Remember React recommends inline styling
