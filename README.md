# Node-RED Workshop starter

Different ways to start a workshop, either locally, using MiniShift or using IBM Cloud.

**Before you begin**

Before setting up your environment, and in order to create the services needed for the workshop, you'll need an IBM Cloud account. 

1. [Sign up for an account here](https://cloud.ibm.com/registration)
2. Verify your account by clicking on the link in the email sent to you

## Run Node-RED using IBM Cloud

1. Log in to your [IBM Cloud account](http://cloud.ibm.com)
2. Click on "Catalog" at the top-right corner
3. Search and select "Node-RED Starter" 
4. Give a unique name to your app and click "Create"
5. Once your app is created you'll be able to access it through the [resource list](https://cloud.ibm.com/resources)

## Run Node-RED using MiniShift

This requires having Minishift installed on your machine, if you don't you can follow a [detailed guide here](https://docs.okd.io/latest/minishift/index.html).

### Background information for Node in OpenShift

The OpenShift `nodejs` cartridge documentation can be found at:

http://openshift.github.io/documentation/oo_cartridge_guide.html#nodejs

### 1. Get Minishift running

Open your terminal and run the following commands: 
1. `minishift --vm-driver=hyperkit start` (or equivalent, based on which vm you're using)
2. `eval $(minishift oc-env)` 
3. `oc login -u developer`

### 2. Get OpenShift-Node-RED

**Clone the repository**

Still using your terminal:
1. Clone the [starter repo](https://github.com/emarilly/openshift-node-red) using the following command:
- `git clone https://github.com/emarilly/openshift-node-red`
2. Change directory into the OpenShift-noe-red folder
- `cd openshift-node-red`

**Update package.json**

Using your favorite IDE (we're not judging) open and edit the `package.json` file:
1. update the Node.js engine to [current LTS](https://nodejs.org/en/download/) (`>=10.0.0`)
2. update the NPM engine to be inline with node LTS (`>= 6.0.0`)
3. update Node-RED dependency to current levels (`>= 0.20.0`)
4. add a scripts section
  ```
  "scripts": {
    "build": "true",
    "start": "node server.js"
  },
  ```

**Update server.js**

Next, open and edit the `server.js` file:
1. comment out basic authentication setup
  ```
  //setup basic authentication
  //var basicAuth = require('basic-auth-connect');
  //self.app.use(basicAuth(function(user, pass) {
  //    return user === 'test' && pass === atob('dGVzdA==');
  //}));
  ```
2. update local port to `8080`
  ```
  self.port      = process.env.OPENSHIFT_NODEJS_PORT || 8080;
  ```
3. update default ipaddress to "0.0.0.0"
  ```
  self.ipaddress = process.env.OPENSHIFT_NODEJS_IP || "0.0.0.0";
  ```

Once this is all done, go back to your terminal and run the following command `npm install`.

### Push Node-RED on Minishift

1. Create a new app and give it a name (ie. myfirst-node-red)
- `oc new-app nodejs~./ --name=myfirst-node-red`
2. Start the build of your app 
- `oc start-build myfirst-node-red --from-dir=./`
3. You can check the status at any time using
- run `oc status` 
4. Once the build is complete, run the following command to get the service
- `oc get service myfirst-node-red`
5. Expose the service at a host name using:
- `oc expose service myfirst-node-red`

All done! You can now access your Node-RED application running on Minishift using the response from `oc get route/myfirst-node-red`

## Run Node-RED locally

Follow the instructions on the [Node-RED website](https://nodered.org/docs/getting-started/local)


You're all set and ready to go! Go back to the workshop page and follow the instructions there.



## Troubleshooting

1. When building your app to run on Minishift the build may fail on the first go, try running the following command again `oc start-build myfirst-node-red --from-dir=./`
