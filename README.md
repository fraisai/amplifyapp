# amplifyapp
Repo is connected to AWS Amplify web hosting and it will be deployed to a globally available CDN hosted on an amplifyapp.com domain and will be set up for continuous deployment.

AWS Amplify is an AWS service that lets you build full stack apps quickly - it's primarily used as a CLI Tool, lets you quickly add Storage, Authentication, Monitoring, etc.

# Part 1
1. I created a React app
```
npx create-react app amplifyapp
```

2. Initialized this GitHub repo and committed app to this repo
```
git remote add origin *****
git branch -M main
git push origin -u origin main
```

3. Selected the AWS Amplify service console on the AWS Management Console

4. Connected this GitHub repo I just created to the AWS Amplify service in order to build, deploy, and host my app on AWS (selected Get Started under Amplify Hosting > selected GitHub > Authenticated with GitHub > chose the repo and main branch I created > Next > Accepted the default build settings > Next > Save and Deploy).

5. By this point, I have deployed a React app in the AWS Cloud by integrating with GitHub and using AWS Amplify, which continuously deploys my app in the cloud and hosts it on a globally available CDN and any edits I make to the files in my src folder that I push the changes of to the main branch of my repo will automatically initiate a new build in the hosted app on AWS Amplify.

# Part 2

1. I installed the Amplify CLI globally. The Amplify CLI is a unified toolchain to create AWS Cloud services for my app following a simple, guided workflow
```
npm install -g @aws-amplify/cli
```

2. I configured the Amplify CLI and created a new IAM user (amplify-user) - used the [following instructions](https://www.youtube.com/watch?v=fWbM5DLh25U).
```
amplify configure
```

3. Deployed a backend and initialized the backend environment locally (selected Backend environments > Get Started > Launch Studio > expand the Local setup instructions section and ran the command to use the Amplify CLI in root folder)

```
? Choose your default editor: Visual Studio Code
? Choose the type of app that you're building javascript
? What javascript framework are you using react
? Source Directory Path:  src 
? Distribution Directory Path: build
? Build Command:  npm run-script build
? Start Command: npm run-script start
? Do you plan on modifying this backend? Y
```

To view your Amplify project in the dashboard at any time, you can now run the following command:
```
amplify console
```

# Part 3
I added authentication - authenticate a user with the Amplify CLI and libraries, leverage Amazon Cognito, a managed user identity service, use the Amplify UI component library to scaffold out an entire user authentication flow, allowing users to sign up, sign in, and reset their password with just few lines of code.
- Amplify libraries – The Amplify libraries allow you to interact with AWS services from a web or mobile application.
- Authentication – In software, authentication is the process of verifying and managing the identity of a user using an authentication service or API.

1. Installed the Amplify libraries in the root directory. We will need two Amplify libraries for our project.
- main aws-amplify library contains all of the client-side APIs for interacting with the various AWS services we will be working with and
- @aws-amplify/ui-react library contains framework-specific UI components.
```
npm install aws-amplify @aws-amplify/ui-react
```

2. Created the authentication service using Amplify CLI
```
amplify add auth

? Do you want to use the default authentication and security configuration? Default configuration
? How do you want users to be able to sign in? Username
? Do you want to configure advanced settings? No, I am done.
```

3. Deployed the authentication service. After the authentication service has been configured locally, it can be deployed by running the Amplify push command
```
amplify push --y
```

4. Configure the React project with Amplify resources
The CLI has created and will continue to update a file called aws-exports.js located in the src directory of our project. I used this file to let the React project know about the different AWS resources that are available in my Amplify project.

To configure my app with these resources, I opened src/index.js and added the following code below the last import:
```
import { Amplify } from 'aws-amplify';
import config from './aws-exports';
Amplify.configure(config);
```

5. I added the authentication flow in App.js by doing the following:
Opened src/App.js and updated it with the following code:
```
import logo from "./logo.svg";
import "@aws-amplify/ui-react/styles.css";
import {
  withAuthenticator,
  Button,
  Heading,
  Image,
  View,
  Card,
} from "@aws-amplify/ui-react";

function App({ signOut }) {
  return (
    <View className="App">
      <Card>
        <Image src={logo} className="App-logo" alt="logo" />
        <Heading level={1}>We now have Auth!</Heading>
      </Card>
      <Button onClick={signOut}>Sign Out</Button>
    </View>
  );
}

export default withAuthenticator(App);
```

In this code, I used the withAuthenticator component. This component will scaffold out an entire user authentication flow allowing users to sign up, sign in, reset their password, and confirm sign-in for multifactor authentication (MFA). I also used the AmplifySignOut component which will render a Sign Out button.

6. Run the app locally to see the new Authentication flow that protects the app
```
npm start
```

you can try signing up, which will send a verification code to your email. Use the verification code to log in to the app. When signed in, you should see a Sign Out button, which signs you out and restarts the authentication flow.

7. Set up the CI/CD of the frontend and backend

In order to configure the Amplify build process to add the backend as part of the continuous deployment workflow, from my terminal window, I ran to open the Amplify console:
```
amplify console
```

From the navigation pane, I chose App settings > Build settings and modified it to add the backend section to my amplify.yml:
```
backend:
  phases:
    build:
      commands:
        - '# Execute Amplify CLI with the helper script'
        - amplifyPush --simple

```

So the full amplify.yml file looked like the following:
```
version: 1
backend:
  phases:
    build:
      commands:
        - '# Execute Amplify CLI with the helper script'
        - amplifyPush --simple
frontend:
  phases:
    preBuild:
      commands:
        - yarn install
    build:
      commands:
        - yarn run build
  artifacts:
    baseDirectory: build
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*

```

Then I scrolled down to Build image settings and chose Edit. Selected the Add package version override dropdown and selected Amplify CLI. It should default to the latest version.

Next, I updated my frontend branch to point to the backend environment I just created. Under the branch name, I chose Edit, and then pointed my main branch to the staging backend I just created. Save.

Followed this link to set up new role for backend: https://docs.aws.amazon.com/amplify/latest/userguide/how-to-service-role-amplify-console.html and added the new permissions to amplifyapp

8. Deployed the changes to the live environment





