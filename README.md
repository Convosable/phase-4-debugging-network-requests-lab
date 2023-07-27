# Putting it All Together: Client-Server Communication

## Learning Goals

- Understand how to communicate between client and server using fetch, and how
  the server will process the request based on the URL, HTTP verb, and request
  body
- Debug common problems that occur as part of the request-response cycle

## Introduction

Just like the last lesson, we've got code for a React frontend and Rails API
backend set up. This time though, it's up to you to use your debugging skills to
find and fix the errors!

To get the backend set up, run:

```console
$ bundle install
$ rails db:migrate db:seed
$ rails s
```

Then, in a new terminal, run the frontend:

```console
$ npm install --prefix client
$ npm start --prefix client
```

Confirm both applications are up and running by visiting
[`localhost:4000`](http://localhost:4000) and viewing the list of toys in your
React application.

## Deliverables

In this application, we have the following features:

- Display a list of all the toys
- Add a new toy when the toy form is submitted
- Update the number of likes for a toy
- Donate a toy to Goodwill (and delete it from our database)

The code is in place for all these features on our frontend, but there are some
problems with our API! We're able to display all the toys, but the other three
features are broken.

Use your debugging tools to find and fix these issues.

There are no tests for this lesson, so you'll need to do your debugging in the
browser and using the Rails server logs and `byebug`.

**Note**: You shouldn't need to modify any of the React code to get the
application working. You should only need to change the code for the Rails API.

As you work on debugging these issues, use the space in this README file to take
notes about your debugging process. Being a strong debugger is all about
developing a process, and it's helpful to document your steps as part of
developing your own process.

## Your Notes Here

- Add a new toy when the toy form is submitted

  - How I debugged:  
    Error message: POST http://localhost:4000/toys 500 (Internal Server Error)

    Since its a 500 error, there must be a problem with the server, so I checked the server logs in the terminal.

    Message from console: NameError (uninitialized constant ToysController::Toys):
    app/controllers/toys_controller.rb:10:in `create'

    Went to the ToysController and saw this for create:
      def create
        toy = Toys.create(toy_params)
        render json: toy, status: :created
      end

    The problem is a typo (s at the end of Toy)... it should be:
    toy = Toy.create(toy_params)

    Error resolved!



- Update the number of likes for a toy

  - How I debugged:
    Error message: Uncaught (in promise) SyntaxError: Unexpected end of JSON input at ToyCard.js:27:1

    Since its an Unexpected end of JSON error, I checked the fetch request and the controller to see if any json is being returned.

    Went to the ToysController and saw this for update:
    def update
      toy = Toy.find_by(id: params[:id])
      toy.update(toy_params)
    end

    There is noo json being rendered after updating the toy, so no data is being sent back to the front end.

    To fix, we add the line render json: toy, status: :accepted.

    Error resolved!



- Donate a toy to Goodwill (and delete it from our database)

  - How I debugged:
    Error message: DELETE http://localhost:4000/toys/9 404 (Not Found)

    Since its a 400 error, I check the network tab to see if there is a message with more details.
    Under preview I see:
    error
    : 
    "Not Found"
    exception
    : 
    "#<ActionController::RoutingError: No route matches [DELETE] \"/toys/9\">"
    status
    :
    404

    So there is an issue in my routung, so I check routes.rb in config and find:
    resources :toys, only: [:index, :create, :update]

    There is no route set up for destroy.
    To fix, add :destroy after update.
      resources :toys, only: [:index, :create, :update, :destroy]

    Error resolved!