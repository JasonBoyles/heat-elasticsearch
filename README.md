Description
===========

This is a template for deploying an [Elasticsearch](http://www.elasticsearch.org/)
clusterwith [OpenStackHeat](https://wiki.openstack.org/wiki/Heat) on the [Rackspace
Cloud](http://www.rackspace.com/cloud/). This template uses
[chef-solo](http://docs.opscode.com/chef_solo.html) to configure the servers.

Requirements
============
* A Heat provider that supports the Rackspace `OS::Heat::ChefSolo` plugin.
* An OpenStack username, password, and tenant id.
* [python-heatclient](https://github.com/openstack/python-heatclient) `>= v0.2.8`:

```bash
pip install python-heatclient
```

We recommend installing the client within a [Python virtual
environment](http://www.virtualenv.org/).

Example Usage
=============
Here is an example of how to deploy this template using the
[python-heatclient](https://github.com/openstack/python-heatclient):

```
heat --os-username <OS-USERNAME> --os-password <OS-PASSWORD> --os-tenant-id \
  <TENANT-ID> --os-auth-url https://identity.api.rackspacecloud.com/v2.0/ \
  stack-create elasticsearch-stack -f elasticsearch.yaml \
  -P es_node_count=4
```

* For UK customers, use `https://lon.identity.api.rackspacecloud.com/v2.0/` as
the `--os-auth-url`.

Optionally, set environmental variables to avoid needing to provide these
values every time a call is made:

```
export OS_USERNAME=<USERNAME>
export OS_PASSWORD=<PASSWORD>
export OS_TENANT_ID=<TENANT-ID>
export OS_AUTH_URL=<AUTH-URL>
```

Parameters
==========
Parameters can be replaced with your own values when standing up a stack. Use
the `-P` flag to specify a custom parameter.

* `load_balancer_hostname`: Sets the name of the load balancer. (Default: es-lb)
* `server_hostname`: Configures the hostname for the Elasticsearch nodes (Default: es)
* `flavor`: Cloud server flavor to use. (Default: 2 GB Performance)
* `ssh_keypair_name`: Name of the SSH key pair to register with nova (Default:
  elasticsearch)
* `es_auth_user`: userid for HTTP basic authentication when connecting to the
  Elasticsearch service. This will also apply to connections made via the
  load balancer. (Default: es_user)

Outputs
=======
Once a stack comes online, use `heat output-list` to see all available outputs.
Use `heat output-show <OUTPUT NAME>` to get the value fo a specific output.

* `es_server_ips`: Public IP addresses for the Elasticsearch nodes created.
* `private_key`: SSH private key that can be used to login as root to the servers.
* `es_auth_user`: userid for HTTP basic auth
* `es_password`: generated password for HTTP basic auth
* `load_balancer_ip`: Public IP address of the cloud load balancer

For multi-line values, the response will come in an escaped form. To get rid of
the escapes, use `echo -e '<STRING>' > file.txt`. For vim users, a substitution
can be done within a file using `%s/\\n/\r/g`.

Stack Details
=============
Each elasticsearch instance is proxied via nginx on port 8080, with HTTP basic
authentication, a userid as configured by the parameter `es_auth_user`, and
a random password generated by the Heat engine.

The load balancer is configured to accept connections on port 8080 and
distribute connections round-robin to the Elasticsearch nodes.

Contributing
============
There are substantial changes still happening within the [OpenStack
Heat](https://wiki.openstack.org/wiki/Heat) project. Template contribution
guidelines will be drafted in the near future.

License
=======
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
