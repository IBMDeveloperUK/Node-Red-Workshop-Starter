# Adjusting Minishift config to work with Code Ready Containers

OpenShift moves on but Minishift does not - it will end its usefulness at OpenShift V3.11

If you want to setup a local OpenShift V4.*, [Code-Ready-Containers](https://github.com/code-ready/crc) is the way forward.

Following the instructions in the [Installation Guide](https://code-ready.github.io/crc/) to get a running cluster.

Once you have a working cluster, you can run the `eval $(crc oc-env)` command to establish the environment for the `oc` command line tool.

The process is very similar to that for Minishift, but explicitly uses a binary build to create the application image from the
folder, without trying to connect to the original remote git repo.

```
oc new-build nodejs --binary --name=mycrc-node-red
oc start-build mycrc-node-red --from-dir=.
```
Once the first build has completed, then run:
```
oc new-app mycrc-node-red
oc expose svc mycrc-node-red
```

As for Minishift, when you make changes to the base NodeRed config and want to rebuild, run:
```
oc start-build mycrc-node-red --from-dir=.]
```

You will find the URL for your new application by runnning:
```
oc get route mycrc-node-red
```
which will return something similar to:

```
NAME             HOST/PORT                             PATH   SERVICES         PORT       TERMINATION   WILDCARD
mycrc-node-red   mycrc-node-red-rdc.apps-crc.testing          mycrc-node-red   8080-tcp                 None
```

