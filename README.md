# Movies Sample app

This example shows how to leverage to develop a Node.js + React Sample App directly in Kubernetes. The Node + React Sample App is deployed using a [Helm Chart](https://github.com/okteto/movies/tree/master/chart). It creates the following components:

- A *React* based front-end, using [webpack](https://webpack.js.org) as bundler and *hot-reload server* for development.
- A very simple Node.js API using [Express](https://expressjs.com).
- A Coder.com IDE to develop the API component.
- A [MongoDB](https://www.mongodb.com) database.


## Step 1: Deploy the Movies App

Click on the next button to deploy the Movies App development environment:

[![Develop on Okteto](https://okteto.com/develop-okteto.svg)](https://cloud.okteto.com/deploy?repository=https://github.com/pchico83/movies)

The [Movies App](https://github.com/okteto/movies) is a simple web app composed of: a React frontend, a Node.js api, and a mongodb database.

When you deploy the Movies App Git repository, Okteto Cloud will run the deploy pipeline defined by the `okteto-pipeline.yml` of the repository.
In this case, the pipeline deploys the chart of the Movies App with Helm.

> Check the [docs](/docs/cloud/okteto-pipeline) to know more about how to configure your `okteto-pipeline.yml` file.

At this point, your application is being deployed. It will be ready to go once its components reach the `Running` state.

<p align="center"><Image src="images/ui-cloud.png" width="850" /></p>

The Okteto Cloud dashboard is designed to be developer friendly, it gives you instant feedback about your applications logs, errors, metrics and more.

Now click on the application's endpoint of the frontend component to access the Movies App. You should see something like this:

<p align="center"><Image src="images/ui-movies.png" width="850" /></p>

Cool no 😎? But ooh... seems like we have a bug in our application. The list of *Movies* and the list of *Continue watching for Cindy* are the same. Let us work on a fix now!

## Step 2: Install the Okteto CLI

Okteto provides a local development experience for Kubernetes applications. You code locally in your favorite IDE and Okteto automatically updates in seconds your application running in Kubernetes. No commit, build, push or deploy required.

>The Okteto CLI is open source, and the code is available at [Github](https://github.com/okteto/okteto). It is a client-side only tool that works in any Kubernetes cluster. Check it out and star it to show your support 🤗!

If you haven't done it yet, install the Okteto CLI:

<Tabs
  defaultValue="mac-linux"
  values={[
    { label: 'MacOS / Linux', value: 'mac-linux', },
    { label: 'Windows', value: 'windows', },
  ]}
>
<TabItem value="mac-linux">

```console
$ curl https://get.okteto.com -sSfL | sh
```

</TabItem>
<TabItem value="windows">

Download https://downloads.okteto.com/cli/okteto.exe and add it to your `$PATH`.

</TabItem>
</Tabs>

More installation options are documented [here](/docs/getting-started/installation).

## Step 3: Configure access to your Okteto Cloud namespace

The Okteto CLI can download your Kubernetes credentials from Okteto Cloud. To do this, you just need to run the namespace command:

```console
$ okteto namespace
```

```console
Authentication required. Do you want to log into Okteto? [y/n]: y
What is the URL of your Okteto instance? [https://cloud.okteto.com]: 
Authentication will continue in your default browser
 ✓  Logged in as cindy
 ✓  Updated context 'cloud_okteto_com' in '/Users/cindy/.kube/config'
```

The `okteto namespace` command adds your Kubernetes credentials to your kubeconfig file, and sets it as the current context.
If you're not logged into Okteto Cloud yet, it also runs the [login sequence](docs/reference/cli#login).
Once you do this, you will have full access to your Kubernetes namespace with any Kubernetes tool.

## Step 4: Activate your development container

Clone the repo with the source code of the Movies App:

```console
$ git clone https://github.com/okteto/movies
$ cd movies
```

And activate your development container to develop on the api component:

```console
$ cd api
$ okteto up
```

```console
 ✓  Development container activated
 ✓  Files synchronized
    Namespace: cindy
    Name:      api
    Forward:   8080 -> 8080
               9229 -> 9229

Welcome to your development container. Happy coding!
okteto>
```

The `okteto up` command starts a [development container](https://okteto.com/docs/reference/development-environment) in Okteto Cloud, which means:

- A [file synchronization service](https://okteto.com/docs/reference/file-synchronization) is created to keep your changes up-to-date between your local filesystem and your development container.
- Container ports 8080 (the application) and 9229 (the debugger) are forwarded to localhost.
- A remote shell is started in your development container. Build, test and run your application as if you were in your local machine.

> All of this (and more) can be customized via the `okteto.yml` [manifest file](https://okteto.com/docs/reference/manifest), which lives side by side with the application source code. Use the `.stignore` file to avoid synchronizing build artifacts, git metadata or dependencies like `node_modules`.

Your development container inherits all the Kubernetes configuration of the original api deployment.
For example, the development container receives the database password via an environment variable.
To check it out, execute the following command in the development container shell:

```console
okteto> env | grep MONGODB_PASSWORD
```

```console
MONGODB_PASSWORD=Password!1@2
```

Start the api service by executing the command below in the development container shell:

```console
okteto> yarn start
```

```console
yarn run v1.19.1
$ nodemon server.js
[nodemon] 1.19.2
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] starting `node server.js`
Server running on port 8080.
```

Go back to the browser, and reload the Movies App. Check your development container shell and see how the traffic now goes through your development container:

```console
Server running on port 8080.
GET /api/movies
GET /api/watching
```

## Step 5: Development time!

Let's fix the bug of the Movies App.
Open the `server.js` file in your favorite local IDE and modify the line 41 to query the `watching` collection instead of `movies`:

```javascript
  db.collection('watching').find().toArray( (err, results) =>{
```

Save your changes. Okteto will automatically synchronize your changes to your development container. Take a look at the development container shell and notice how the changes are detected by `nodemon` and automatically hot reloaded.

```console
[nodemon] restarting due to changes...
[nodemon] starting `node server.js`
Server running on port 8080.
```

Go back to the browser, and reload the Movies App. Your code fixes were instantly applied! No commit, build or push required 😎.

## Next steps

Congratulations, you just developed **your first application in Okteto Cloud** 🚀.

> Okteto lets you develop your applications directly in Kubernetes. This way you can:
> - Eliminate integration issues by developing in a realistic environment
> - Test your application end to end as fast as you type code
> - No more CPU cycles wasted in your machine. Develop at the speed of the cloud!

Ready to develop your application on Okteto Cloud? Read our [step by step tutorial](/docs/tutorials/getting-started-with-pipelines) on how to configure an Okteto Pipeline to deploy realistic environments for your application in just one click. 

Head over to our samples for [Go](/docs/samples/golang), [ASP.NET](/docs/samples/aspnetcore), [Java](/docs/samples/java), [Node.js](/docs/samples/node), [PHP](/docs/samples/php), [Python](/docs/samples/python) or [Ruby](/docs/samples/ruby) to see how to use Okteto to live-update your application with different programming languages and **debuggers**.

