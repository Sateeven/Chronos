<a href="https://chronoslany.com/">
    <img src="./app/assets/animated_logo.gif" alt="Chronos" title="Chronos" align="center" height="500" />
</a>


#
![Build Passing](https://img.shields.io/badge/build-passing-blue)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/open-source-labs/Chronos)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Release: 5.2](https://img.shields.io/badge/Release-5.1-orange)


# Chronos 

### :star: Star us on GitHub — it helps! :star:

Chronos is a comprehensive developer tool that monitors the health and web traffic of servers, microservices, and containers. Use Chronos to see real-time data monitoring and receive automated notifications over Slack or email.

## Table of Contents
- [Features](#features)
- [Demo](#demo)
- [Installation](#installation)
    - [Pre-Installation](#pre-installation)
    - [Install Dependencies](#install-dependencies)
    - [Configure Chronos Tracker](#configure-chronos-tracker)
    - [Initialize Chronos Tracker](#initialize-chronos-tracker)
    - [Docker Configuration](#docker-configuration)
    - [Start Chronos](#start-chronos)
    - [Getting the Executable](#getting-the-chronos-executable)
- [Notifications](#notifications)
    - [Slack](#slack)
    - [Email](#email)
- [Branches](#branches)
    - [Master Branch](#master-branch)
      - [Examples](#examples)
        - [Microservices](#microservices)
        - [Docker](#docker)
    - [gRPC Branch](#grpc-branch)
    - [Splash Page](#chronoswebsite)
- [Technologies](#technologies)
- [License](#license)

#
###### Return to [Top](#chronos)
<br>

## <img src ="./app/assets/fire.png" height=22 > What's New? <img src ="./app/assets/fire.png" height=24>

- New Feature
  - Chronos now comes in any OS .exe!
- Overhauled Features
  - Authentication Enabling with Bcrypt
  - Stand-up times decreased
  - Side-by-side Server Comparisons
  - Color-hashing generates unique colors for each server connection
  - Improved navigation bar and buttons
  - Increased overall speed and responsiveness
- Updated Features
  - Added React Testing Library
  - Removed Spectron and Enzyme
  - Added testing suites for unit, integration, and end-to-end testing
  - Refactored components for dependency injection
- Bug Fixes
  - Authentication now functioning properly
## Features 

- Distributed tracing enabled across microservices applications
- Compatible with <img src="./app/assets/graphql-logo-color.png" alt="GraphQL" title="GraphQL" align="center" height="20" /></a>
- Supports <a href="#"><img src="./app/assets/postgres-logo-color.png" alt="PostgreSQL" title="PostgreSQL" align="center" height="20" /></a> and <img src="./app/assets/mongo-logo-color.png" alt="MongoDB" title="MongoDB" align="center" height="20" /></a> databases
- Displays real-time temperature, speed, latency, and memory statistics
- Display and compare multiple microservice metrics in a single graph
#
###### Return to [Top](#chronos)
<br>

## Demo

<a href="#"><img src="./app/assets/enable_sign_up.gif" alt="Chronos-Demo" title="Chronos-Demo" align="center" height="500" /></a></a>

#
###### Return to [Top](#chronos)
<br>

## Installation
This is for the latest Chronos version **5.2 release and later**.

- Stable release: 6.1.0
- LTS release: 6.1.0

### Pre-Installation
Make sure you're running version 14.16.1 of <a href="#"><img src="./app/assets/node-logo-color.png" alt="Node" title="Node" align="center" height="20" /></a></a>, which is the most recent LTS (long-term support) version. 

If you need to roll back from <a href="#"><img src="./app/assets/node-logo-color.png" alt="Node" title="Node" align="center" height="20" /></a> 16.1.0, make sure to run 
```npm rebuild```
in the root directory.

If you're installing Chronos into a microservices application, and you have different folders for each microservice, make sure you also run 
```npm rebuild```
in each microservices folder **after you roll back to version 14.16.1.**

**If you wish to launch the Electron Application in an WSL2 envirronment(Ubuntu) you may need the following commands for an Electron window to appear**

- Install <a href='https://sourceforge.net/projects/vcxsrv/'>VcXsrv</a>

- Run the following command in the terminal

```
sudo apt install libgconf-2-4 libatk1.0-0 libatk-bridge2.0-0 libgdk-pixbuf2.0-0 libgtk-3-0 libgbm-dev libnss3-dev libxss-dev
```

- After running your VcXsrv instance, run the following command in the terminal
```
export DISPLAY="`sed -n 's/nameserver //p' /etc/resolv.conf`:0"
```

<br>

### Install Dependencies

To use Chronos in your existing application, download and install the following in the **root directory** of _each of your microservice applications_:
```
npm install chronos-tracker
```

### Configure Chronos Tracker

Similarly, in the **root directory** of _each of your microservice applications_, create a `chronos-config.js` file with properties listed below:

```js
// A sample `chronos-config.js` file

const chronos = require('chronos-tracker');

chronos.use({
  microservice: 'payments',
  interval: 5000,
  dockerized: true,
  database: {
    connection: 'REST',
    type: 'MongoDB',
    URI: process.env.URI,
  },
  notifications: [],
});
```

The `microservice` property takes in a string. This should be the name of your server or microservice. For **Docker** containers, the name of the microservice should be the same as the name of the corresponding Docker container.

The `interval` property is optional and takes in an integer. This controls the Chronos monitoring frequency. If this is omitted, Chronos will default to recording server health every 60000 ms or 60 seconds.

The `dockerized` property is optional and should be specified as `true` if the server is running inside of a Docker container. Otherwise, this should be `false`. If omitted, Chronos will assume this server is not running in a container.

The `database` property is required and takes in the following:
- `connection` should be a string and only supports 'REST' and 'gRPC'
- `type` should be a string and only supports 'MongoDB' and 'PostgreSQL'.
- `URI` should be a connection string to the database where you intend Chronos to write and record data regarding health, HTTP route tracing, and container infomation.  

_NOTE: A `.env` is recommended._

<!-- - `isDockerized`: Is this microservice running in a Docker container? Enter `yes` or `no`. The current default setting is `no`.
  - <img src="./app/assets/important.png" alt="Important" title="Important" align="center" height="20" /></a> When starting up the container, give it the same name that you used for the microservice, because the middleware finds the correct container ID of your container by matching the container name to the microservice name you input as 1st argument.
  - <img src="./app/assets/important.png" alt="Important" title="Important" align="center" height="20" /></a> Don't forget to bind mount to Docker socket. -->

The `notifications` property is optional. Jump to the section below, [Notifications](#notifications) to configure <a href="#"><img src="./app/assets/slack-logo-color.png" alt="Slack" title="Slack" align="center" height="20" /></a> or email <a href="#"><img src="./app/assets/email-icon-black.png" alt="Slack" title="Slack" align="center" height="20" /></a> notifications.
<br>
<br>

### Initialize Chronos Tracker
#### Initialize Chronos Tracker for REST

Wherever you create an instance of your server (see example below),

```js
// Example for REST
const express = require('express');
const app = express());

```

you will also need to require in `chronos-tracker` and initialize Chronos, as well as the `./chronos-config` file. You will then need to invoke `chronos.propagate()` to initiate the route tracing, in addition to implementing `chronos.track()` for all endpoints.

```js
const chronos = require('chronos-tracker');
require('./chronos-config'); // Bring in config file

// ...

chronos.propagate();
app.use('/', chronos.track());
```

You should be good to go! The last step, **Docker Configuration**, is **only applicable** if you need to configure <a href="#"><img src="./app/assets/docker-logo-color.png" alt="Docker" title="Docker" align="center" height="20" /></a> for your application. 

<br>


#### Initialize Chronos Tracker for gRPC

Wherever you create an instance of your server (see example below),


```js
  // Example of gRPC server
  const server = new grpc.Server();

  server.bindAsync("127.0.0.1:30044", grpc.   ServerCredentials.createInsecure(), () => {
    server.start();
    console.log("Server running at http://127.0.0.1:30044");
});
```
you will also need to require Chronos-tracker, Chronos-config, and dotenv.config(if this is used). For health data, simply use Chronos.track()



```js
//track health data
const chronos = require('chronos-tracker');
require('./chronos-config');
require('dotenv').config(); // set up environment variables in .env
const BookModel = require('./BookModel');

chronos.track()
```
To trace requests, first wrap the gRPC client using Chronos
```js
const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');
const chronos = require('chronos');

const PROTO_PATH = './order.proto';

const packageDefinition = protoLoader.loadSync(PROTO_PATH, {
  keepCase: true,
  longs: String,
  enums: String,
  arrays: true,
});
const OrderToBookService = grpc.loadPackageDefinition(packageDefinition).OrderToBook;
const bookClient = new OrderToBookService('localhost:30044', grpc.credentials.createInsecure());

const ClientWrapper = chronos.ClientWrapper(bookClient, OrderToBookService);
```
Next wrap the gRPC server using Chronos 
```js

  const ServerWrapper = chronos.ServerWrapper(server,  Proto.protoname.service, {
    AddBook: (call, callback) => {
    // console.log(call.metadata)
    // get the properties from the gRPC client call
      const { title, author, numberOfPages, publisher, bookID } = call.request;
    // create a book in our book collection
      BookModel.create({
        title,
        author,
        numberOfPages,
        publisher,
        bookID,
      });
      callback(null, {});
    },
  });

```
For any request you wish to trace, require uuidv4 and write the following code where the initial gRPC request begins,
```js
const require { v4: uuidv4} = require('uuid')
const createMeta = () => {
  const meta = new grpc.Metadata();
  meta.add('id', uuidvd());
  return meta
}
```
and then, invoke createMeta as a third argument to any client method that is the beginning of the request path.

```js
orderClient.AddOrder(
    order,
    (err, data) => {
      if (err !== null) {
        console.log(err);
        // could not add order because bookID does not exist
        return res.sendStatus(404);
      }
      console.log('addOrder response: ', data);
      return res.sendStatus(200);
    },
    createMeta()
  );

```
Finally, on all servers that will be involved in the request path, invoke `chronos.link` with parameters of `client` and `ServerWrapper` in the server wrapper.

```js
chronos.link(client, ServerWrapper);
```

### Docker Configuration

Again, this step is **only applicable** if you are currently using <a href="#"><img src="./app/assets/docker-logo-color.png" alt="Docker" title="Docker" align="center" height="20" /></a> containers for your microservices. 

<a href="#"><img src="./app/assets/important.png" alt="Important" title="Important" align="center" height="20" /></a> Give your containers the same names you pass in as arguments for microservice names.

<a href="#"><img src="./app/assets/important.png" alt="Important" title="Important" align="center" height="20" /></a> In order to have container statistics saved to your database along with other health info, bind volumes to this path when starting up the containers:
```
/var/run/docker.sock
```

For example, you can type the following when starting up a container:
```
docker run -v /var/run/docker.sock:/var/run/docker.sock [your-image-tag]
```

If you're using `docker-compose` to start up multiple containers, you can add a `volumes` key for each of your services in the `docker-compose.yml` file:

```
volumes:
  - "/var/run/docker.sock:/var/run/docker.sock"
```

### Start Chronos

Once you have configured and intialized Chronos Tracker, it will automatically record monitoring data when your servers are running. Finally, start the Chronos desktop app to view that data! After cloning our [GitHub repo](https://github.com/open-source-labs/Chronos), run `npm install` and `npm run both` to start Chronos.
  
### Getting the Chronos Executable  
At the current moment, to get a copy of the executable that works on all OS, the steps are 1) clone the master branch, 2) run `npm i`, 3) run `npm run prepareDist`, 4) run `npm run package-any`, 5) navigate in your Chronos folder `./release-builds/resources` and click on `chronos.exe`. 6) optionally right click to create a shortcut.

#
###### Return to [Top](#chronos)
<br>

## Notifications

The `notifications` property is optional and allows developers to be alerted when the server responds to requests with status codes >= 400. To set up notifications, set the value of the `notifications` property to an array of objects, each with a `type` and `settings` property. 

Chronos only supports **Slack** and **email** notifications.
<br>

### Slack

Chronos uses the <a href="#"><img src="./app/assets/slack-logo-color.png" alt="Slack" title="Slack" align="center" height="20" /></a> API to send messages to a Slack channel and only requires the **webhook url**. Learn how to set up [Slack webhooks](https://api.slack.com/messaging/webhooks) for your team.

An example of configured **slack** settings:

```js
// ...
notifications: [
  {
    type: 'email',
    settings: {
      slackurl: process.env.WEBHOOK
    }
  }
]
// ...
```

### Email
Chronos provides the option to send <a href="#"><img src="./app/assets/email-icon-black.png" alt="Slack" title="Slack" align="center" height="20" /></a> emails. The properties that should be provided are the following
- `emails` - The recipient list (string) can be a single email address or multiple as comma seprated values. 
- `emailHost` - The smtp host (string) of your email server
- `emailPort` - The email port (integer) is either **465** or **587** depending on the sender email security settings. Learn more about email ports by reading the [nodemailer docs](https://nodemailer.com/smtp/)
- `user` - The email address (string) of the sender
- `password` - The password (string) of the sender email

_NOTE: Email notification settings may require alternative security settings to work_
 
An example of configured **email** settings:

```js
// ...
notifications: [
  {
    type: 'email',
    settings: {
      emails: 'foobar@email.com, bizbaz@email.edu',
      emailHost: 'smpt@gmail.com',
      emailPort: 465,
      user: process.env.SENDER_EMAIL,
      password: process.env.SENDER_PASSWORD
    }
  }
]
// ...
```
#
###### Return to [Top](#chronos)
<br>

## Branches

### Master Branch

The **'master'** branch of this repository is where the <a href="#"><img src="./app/assets/electron-logo-color.png" alt="Electron" title="Electron" align="center" height="20" /></a> application for Chronos is deployed. The Chronos monitoring tool includes two database examples, one PostgresQL and one MongoDB, with sample data sets.

**NOTE:** To _replace_ or _delete_ these two databases, simply change the database URIs stored in the following path: 
```
root directory -> electron -> user -> settings.json
```

### _Examples_

We provide two working example microservice applications in the `master` branch for you to test out Chronos: `microservices` and `docker`.

#### _Microservices_

In the `microservices` folder, we provide a sample microservice application that successfully utilizes Chronos to apply all the powerful, built-in features of our  monitoring tool. You can then visualize the data with the <img src="./app/assets/electron-logo-color.png" alt="Electron" title="Electron" align="center" height="20" /></a> app.

Refer to the [README](link) in the `microservices` folder for more details.

#### _Docker_

In the <a href="#"><img src="./app/assets/docker-logo-color.png" alt="Docker" title="Docker" align="center" height="20" /></a> folder within the `master` branch, we provide a sample _dockerized_ microservices application to test out Chronos and to apply distributed tracing across different containers for your testing convenience.

The `docker` folder includes individual <a href="#"><img src="./app/assets/docker-logo-color.png" alt="Docker" title="Docker" align="center" height="20" /></a> files in their respective directories. A docker-compose.yml is in the root directory in case you'd like to deploy all services together.

Refer to the [README](link) in the `docker` folder for more details.

<br>

### gRPC Branch 
The **'gRPC'** branch is the current codebase for the <a href="#"><img src="./app/assets/npm-logo-color.png" alt="NPM" title="NPM" align="center" height="20" /></a> package, which is what you will install in your own application in order to use Chronos. Download the <a href="#"><img src="./app/assets/npm-logo-color.png" alt="NPM" title="NPM" align="center" height="20" /></a> package [here](https://www.npmjs.com/package/chronos-tracker).

## chronosWebsite  
This is the branch that holds the code base for the splash page. Edit the website by first running `git clone -b chronosWebsite https://github.com/open-source-labs/Chronos.git .` and then updating the aws S3 bucket with the changes.

#
###### Return to [Top](#chronos)
<br>

## Technologies

- <a href="#"><img src="./app/assets/electron-logo-color.png" alt="Electron" title="Electron" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/react-logo-color.png" alt="React" title="React" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/js-logo-color.png" alt="JavaScript" title="JavaScript" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/ts-logo-long-blue.png" alt="TypeScript" title="TypeScript" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/postgres-logo-color.png" alt="PostgreSQL" title="PostgreSQL" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/mongo-logo-color.png" alt="MongoDB" title="MongoDB" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/node-logo-color.png" alt="Node" title="Node" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/express-logo-color.png" alt="Express" title="Express" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/http-logo-color.png" alt="HTTP" title="HTTP" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/grpc-logo-color.png" alt="gRPC" title="gRPC" align="center" height="30" /></a> 
- <a href="#"><img src="./app/assets/graphql-logo-color.png" alt="GraphQL" title="GraphQL" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/docker-logo-color.png" alt="Docker" title="Docker" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/aws-logo-color.png" alt="AWS" title="AWS" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/jest-logo-color.png" alt="Jest" title="Jest" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/enzyme-logo-color.png" alt="Enzyme" title="Enzyme" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/spectron-logo-color.png" alt="Spectron" title="Spectron" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/webpack-logo-color.png" alt="Webpack" title="Webpack" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/material-ui-logo-color.png" alt="Material-UI" title="Material-UI" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/vis-logo-color.png" alt="Vis.js" title="Vis.js" align="center" height="30" /></a>
- <a href="#"><img src="./app/assets/plotly-logo-color.png" alt="Plotly.js" title="Plotly.js" align="center" height="30" /></a>
 
#
###### Return to [Top](#chronos)
<br>


## Contributing

Development of Chronos is open source on GitHub through the tech accelerator umbrella OS Labs, and we are grateful to the community for contributing bug fixes and improvements. Read below to learn how you can take part in improving Chronos.

- [Contributing](https://github.com/oslabs-beta/Chronos/blob/master/CONTRIBUTING.md)
#
###### Return to [Top](#chronos)
<br>


## License

Chronos is <a href="#"><img src="./app/assets/mit-logo-color.png" alt="MIT" title="MIT" align="center" height="20" /></a> [licensed.](https://github.com/oslabs-beta/Chronos/blob/master/LICENSE.md) 
#
###### Return to [Top](#chronos)
