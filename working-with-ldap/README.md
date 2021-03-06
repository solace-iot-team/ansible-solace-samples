# Tutorial: Working with LDAP Authorization

- creates a Solace Cloud service or a self-hosted Solace Broker service
- configures the service to use LDAP Authorization

## Prerequisites

- Solace Cloud Account with 1 spare service - if using Solace Cloud service
- JumpCloud service
- test client, e.g. MQTT Box
- install additional python requirements:
  ````bash
  pip install -r requirements.txt
  ````

## Configuring JumpCloud LDAP

Any LDAP Server will work, here we use JumpCloud: https://jumpcloud.com.
Sign up for a free account.

Create an LDAP Directory:
* `JumpCloud LDAP`

Configure the main `service` user:
* `solace_service`
  - password: `Solace123!`
  - bind to LDAP

Configure MQTT client or application users:
* `device_1`
  - password: `device_1_pwd`
* `device_2`
  - password: `device_2_pwd`

Configure a User Group:
* `solace_devices`
  - add `device_1` and `device_2` to the group
  - check the `JumpCloud LDAP` directory is enabled

### Set Environment

````bash
cp template.source.env.sh source.env.sh
vi source.env.sh
  # enter environment values
source source.env.sh
````

### Check JumpCloud Configuration

````bash
./run.check.jumpcloud-config.sh
````

You should see the two devices, similar to this:
````
"msg": [
    "device: device_1",
    "memberOf: cn=solace_devices,ou=Users,o=xxxx,dc=jumpcloud,dc=com"
]
"msg": [
    "device: device_2",
    "memberOf: cn=solace_devices,ou=Users,o=xxxx,dc=jumpcloud,dc=com"
]
````

**Output:**
- ``memberOf`` value, this is the name of the LDAP Authorization Group to be configured on the Broker
````bash
cat tmp/memberOf.yml
````

### Get the JumpCloud Certificate
````bash
./run.get.jumpcloud-cert.sh
````
**Output:**
````bash
cat tmp/jumpcloud.ldap.pem
````

## Create Local Broker Service
````bash
./run.create.local-service.sh
````
**Output:**
- the broker inventory file
````bash
cat tmp/inventory.local.yml
````

## Create Solace Cloud Broker Service
````bash
./run.create.solace-cloud-service.sh
````
**Output:**
- the service inventory file
````bash
cat tmp/inventory.solace_cloud.yml
````

## Configure the Broker(s)

````bash
./run.configure.brokers.sh
````

**Output:**
- on screen: plain mqtt & http connection details for the broker(s)
- file: all vpn connection details
````bash
cat tmp/info.solace_cloud.vpn-client-connection-details.yml
cat tmp/info.local.vpn-client-connection-details.yml
````

## Run Tests
````bash
./run.test.brokers.sh
````

**Output:**
- on screen: all tests passed

## Teardown Brokers
````bash
./run.teardown.brokers.sh
````

---
The End.
