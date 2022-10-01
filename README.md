# Adding React to Flask

## Learning Goals

- Use React and Flask together to build beautiful and powerful web applications.
- Organize client and server code so that it is easy to understand and maintain.

***

## Key Vocab

- **Full-Stack Development**: development of a frontend and a backend for an
  application. True full-stack development includes a database, a logic/server
  layer, and a frontend built in JavaScript, HTML, and CSS.
- **Backend**: the layer of a full-stack application that handles business logic
  and other programmatic tasks that users do not or should not see. Can be
  written in many languages, including Python, Java, Ruby, PHP, and more.
- **Frontend**: the layer of a full-stack application that users see and
  interact with. It is always written in the frontend languages: JavaScript,
  HTML, and CSS. (_There are others now, but they are based on these three._)
- **Cross-Origin Resource Sharing (CORS)**: a method for a server to indicate
  any ports (or other identifiers) for servers that can share its resources.
- **Transmission Control Protocol (TCP)**: a protocol that defines how computers
  send data to each other. A connection is formed and stars active until the
  applications on either end have finished sending data to one another.
- **Hypertext Transfer Protocol (HTTP)**: a stateless protocol where
  applications communicate for the length of time that it takes for data to be
  transferred.
- **Websocket**: a protocol that allows clients and servers to communicate with
  one another in both directions. The bidirectional nature of websocket
  communication allows a connected state to be generated and the connection
  maintained until it is terminated by one side. This allows for speedy and
  seamless connections between frontends and backends.

***

## Introduction

Earlier in this phase, we used React and Flask together for two different
applications: Chatterbox, a messenger with CRUD functionality, and Plantsy,
a plant shop with CRUD functionality through a RESTful backend. In these
labs, we focused on the server-side (Python) code. Now, let's take a closer
look at the JavaScript that creates the user interface.

***

## Setup

This lesson contains the solution code from the Chatterbox lab. To run the
application, open two terminal windows. In the first, enter the `server/`
directory and run:

- `pipenv install && pipenv shell` to enter your virtual environment.
- `export FLASK_APP=app.py` and `export FLASK_RUN_PORT=5555` to configure your
  Flask environment.
- `flask db upgrade` to generate your database.
- `python seed.py` to populate it.
- `python app.py` to run your development server.

In the second window, enter the `client/` directory and run:

- `npm install` to retrieve the React project's dependencies.
- `npm start` to start your development server and open the application.

> **NOTE: There's a lot more to keep track of now! When you have to run several
> commands to start working, it's useful to write scripts to automate the
> startup process. Refer back to "Configuring Python Applications" in Phase 3
> if you need help getting started! Don't worry about messing things up- you can
> always re-fork the lesson if you need to.**

In your browser, you should see the Chatterbox app in all its glory:

![screenshot of chatterbox app with purple header bar and messages from several
users](
https://curriculum-content.s3.amazonaws.com/python/python-p4-adding-react-to-flask-chatterbox.png)

Once you've confirmed that the application is running correctly, open up the
`client/` directory to explore our JavaScript code.

***

## React `fetch()`

React uses a function called `fetch()` to retrieve data from APIs at other URLs.
In order to get data with fetch, we put the command inside of a `useEffect` hook
as seen in `client/src/components/App.js`. We include an empty array as a second
argument (dependencies) to tell `useEffect` to only run `fetch()`, an
asynchronous operation, on the first render of `App`.

```js
// client/src/components/App.js

function App() {
  ...
  useEffect(() => {
    fetch("http://127.0.0.1:5555/messages")
      .then((r) => r.json())
      .then((messages) => setMessages(messages));
  }, []);
  ...
```

As we can see, this has the React application looking for a resource at
`http://127.0.0.1:5555/messages`: our Flask API. The response data is `then()`
converted to JSON if it is not already in that format, `then()` that data is
used to populate the application with messages.

We can throw much more into our chain here- for instance, if we're looking for a
"200: OK" response from the server:

```js
// client/src/components/App.js

function App() {
  ...
  useEffect(() => {
    fetch("http://127.0.0.1:5555/messages")
      .then(r => {
        if (r.ok) {
          return r.json
        }
        throw r;
      })
      .then((messages) => setMessages(messages))
  }, []);
  ...
```

It is wise when developing full-stack applications to look for the correct data
formats and response codes from the server as you move forward. Since you wrote
the backend yourself, this is _much_ easier than doing so while `fetch()`ing
data from someone else's API and will help your users understand how to avoid
errors in the future. In the example above, we simply `throw r` because it
receives any error message we catch and send forward from our Flask backend!

### `POST`, `PATCH`, `DELETE` with `fetch()`

`fetch()` defaults to using `GET` as its HTTP method. If we want to carry out
functions other than basic retrieves, we need to specify that and our message
format

```js
//client/src/components/NewMessage.js

function NewMessage({ currentUser, onAddMessage }) {
  const [body, setBody] = useState("");

  function handleSubmit(e) {
    e.preventDefault();

    fetch("http://127.0.0.1:5555/messages", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        username: currentUser.username,
        body: body,
      }),
    })
      .then((r) => r.json())
      .then((newMessage) => {
        onAddMessage(newMessage);
        setBody("");
      });
  }
  ...

```

Rather than `useEffect`, here we have a hook called `onAddMessage` passed to the
`NewMessage` element that updates the entire app when a message is confirmed to
have been created. `handleSubmit()` is invoked each time a user hits the "Send" button
and generates a `POST` request with the body of the `NewMessage` element.

`fetch()` takes an optional object as a second argument after the Flask API's
URL with an HTTP request method, headers to specify the format of the message,
and a message body. (`PATCH` requests need these elements as well!)

After generating a new record in the database, Flask returns a response that
is assigned to `r` and converted to JSON if necessary. Finally, we invoke
`onAddMessage` to update the app with this new message and reset the form to
be empty and ready for new input.

```py
# python code block
print("statement")
# => statement
```

```js
// javascript code block
console.log("use these for comparisons between languages.")
// => use these for comparisons between languages.
```

```console
echo "bash/zshell statement"
# => bash/zshell statement
```

<details>
  <summary>
    <em>Check for understanding text goes here! <code>Code statements go here.</code></em>
  </summary>

  <h3>Answer.</h3>
  <p>Elaboration on answer.</p>
</details>
<br/>

***

## Instructions

This is a **test-driven lab**. Run `pipenv install` to create your virtual
environment and `pipenv shell` to enter the virtual environment. Then run
`pytest -x` to run your tests. Use these instructions and `pytest`'s error
messages to complete your work in the `lib/` folder.

Instructions begin here:

- Make sure to specify any class, method, variable, module, package names
  that `pytest` will check for.
- Any other instructions go here.

Once all of your tests are passing, commit and push your work using `git` to
submit.

***

## Conclusion

Conclusion summary paragraph. Include common misconceptions and what students
will be able to do moving forward.

***

## Resources

- [Resource 1](https://www.python.org/doc/essays/blurb/)
- [Reused Resource][reused resource]

[reused resource]: https://docs.python.org/3/
