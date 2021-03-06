# OpenShift Provider - Atomic App CLI Execution

### Overview

Applications started using the OpenShift provider will result in
applications running in an OpenShift environment after execution.
Executing Atomic Apps using the Atomic CLI uses Atomic App code to
talk to a remote (or local) OpenShift instance via API calls to start
the application.

One piece of the puzzle is telling Atomic App how to communicate with
the OpenShift master. This can be done in one of two ways.

1. Passing in the `providerconfig` value in answers.conf
2. Passing in both the `providerapi` and `accesstoken` values in answers.conf.

These config items are detailed below.


### Configuration

#### namespace

The OpenShift provider supports namespaces. The default
namespace for an application is set to `default` unless otherwise
specified in the answers.conf. It can be changed in the `[general]`
section of the answers.conf file. An example is below:

```
[general]
namespace: mynamespace
```

**NOTE**: If there is a namespace value set in the artifact metadata
then that value will always be used and won't be overridden.

#### providerconfig

For OpenShift, one way to let Atomic App know how to communicate with
the master is by re-using the provider config file that already exists
on a user's machine.  Basically whatever the user can do with the `oc`
command, Atomic App can do by re-using the same provider config.

One example of specifying a `providerconfig` is below:

```
[general]
provider = openshift
providerconfig = /home/user/.kube/config
```

#### providerapi + accesstoken

Another to pass credential information in is by passing in both the
location where the openshift API is being served, as well as the
access token that can be used to authenticate. These are done with the 
`providerapi` and `accesstoken` config variables in the `[general]`
section within answers.conf.

An example of this is below:

```
[general]
provider = openshift
providerapi = https://10.1.2.2:8443
accesstoken = sadfasdfasfasfdasfasfasdfsafasfd
namespace = mynamespace
```

**NOTE**: You can get the session token from console by running 
`oc whoami -t` or if you are not using `oc` client you can get it 
via web browser on `https://<openshift server>/oauth/token/request`

#### providertlsverify
If `providerapi` is using https protocol you can optionally
disable verification of tls/ssl certificates. This can be especially
useful when using self-signed certificates.

```
[general]
provider = openshift
providerapi = https://127.0.0.1:8443
accesstoken = sadfasdfasfasfdasfasfasdfsafasfd
namespace = mynamespace
providertlsverify = False
```

**NOTE**: If `providerconfig` is used  values of `providertlsverify`
and `providercafile` are set according to settings in `providerconfig` file.

#### providercafile
If `providerapi` is using https protocol you can optionally specify
path to a CA_BAUNDLE file or directory with certificates of trusted CAs.

**NOTE**: If `providerconfig` is used  values of `providertlsverify`
and `providercafile` are set according to settings in `providerconfig` file.


#### Configuration Value Defaults

Table 1. OpenShift default configuration values

Keyword  | Required | Description                                             | Default value
---------|----------|---------------------------------------------------------|--------------
namespace|   no     | namespace to use with each kubectl call                 | default
providerconfig| no  | config file that specifies how to connect to kubernetes | none
providerapi|  no    | the API endpoint where API requests can be sent         | none
accesstoken|  no    | the access token that can be used to authenticate       | none
providertlsverify|no| turn off verificatoin of tls/ssl certificates           | False
providercafile| no  | path to file or directory with trusted CAs              | none

**NOTE**: One of `providerconfig` or `providerapi` + `accesstoken` are required

**NOTE**: Namespace can be set in the file pointed to by `providerconfig` or 
in the `answers.conf`. If it is set in both places then the values must match,
or an error will be reported.


### Operations

```
atomicapp run
```

This command deploys the app in the OpenShift cluster in a specified namespace. 
For the given namespace, the deploy process creates objects (pods, replicas, 
services) in order as enlisted in Nulecule OpenShift artifacts.

```
atomicapp stop
```
This command undeploys the app in the OpenShift cluster in a specified namespace. 
For the given namespace, the undeploy process consist of:

  1. Scaling down all replicas to 0

  2. Deleting all objects (pods, replicas, services)
