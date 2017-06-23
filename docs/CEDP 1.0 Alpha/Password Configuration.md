# Password Configuration

This document describes how to change admin password and restricted user account passwords for for services within the CEDP


#### Table of Contents

- Graylog
  * Admin
  * Restricted users
- Grafana
  * Admin
  * Restricted users
- Zabbix
  * Admin


### Graylog
Graylog is used to view messages from docker containers and kubernetes system services. These messages can be used to track usage metrics, errors, and anything else the creators would want to track.

##### Graylog Admin
The Graylog admin password is located in the graylog.conf file found at /data/gl-config/graylog.conf

The admin user is 'admin' by default and can be changed by uncommenting the line and changing the value to the desired administrator username.

`# The default root user is named 'admin'`

`#root_username = admin`

The password is located below the admin username with the key 

`root_password_sha2 = 8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918`

To change the admin password run the command `echo -n yourpassword | shasum -a 256` on the command line where *'yourpassword'* is the desired password.  Paste the output into the config file after *'root_password_sha2 = '*

##### Graylog Restricted Users

Restricted users are locked to a role which has access to specific queries and/or dashboards. The following example creates a dashboard that shows all messages in the cedp namespace and locks a user to that dashboard and no other data.

Log in to the Graylog dashboard located at http://`<ip address>`:30081.
1. Click "Streams" from the top bar.
2. Click "Create Stream" and enter in all details leaving the index value as default then press "Save"
3. Click "Manage Rules" next to the newly created stream. Select the GELF input from the "Select an input" field and then click "Add stream rule".
4. In the new windows enter "Namespace" in the _field_ field and the namespace for the new user in the _value_ field.
5. Click "Save" and then "I'm done".
6. Click the name of the new Stream to get the the search screen for that stream.
7. On the top bar select "Dashboards". 
8. Click "Create Dashboard" and give the dashboard a descriptive title and description then click "Save".
9. At the top of the screen the 'System' dropdown and select 'Authentication'
10. Click "Roles" on the left.
11. Click "Add new role" on the right and give the role a descriptive title and description.
12. Beside the the Namespace's stream select "Allow Reading" and "Allow Editing"
13. Change the the dashboard tab and select the newly created dashboard and click "Allow Reading" and "Allow Editing" on the right then click "Save".
14. Choose users from the left menu.
15. Click "Add new user"
16. Enter a username and password and all other relevant information
17. In the roles box select the newly created role.



### Grafana
Grafana displays the resource usage metrics gathered by Heapster which talked directly to cAdvisor in the kubernetes system.

###### Grafana Admin
Log in to the Grafana dashboard located at http://`<ip address>`:30083 as the administrator
1. Click the icon in the very top left corner which shows a dropdown menu then select "Admin" second from the bottom and click "Global Users".
2. Click "Edit" next to the admin user.
3. On the next page change the admin password and any other information you wish to change and click "Update"

###### Grafana Restricted Users
Log in to the Grafana dashboard located at http://`<ip address>`:30083 as the administrator
1. Click the icon in the very top left corner which shows a dropdown menu then select "Admin" second from the bottom and click "Global Users".
2. On the next screen click "Add new user"
3. Enter the username and password for the new user and then click "Create"
4. In the users list click "Edit" Beside the newly created user.
5. At the bottom under "Roles" change the role to view and click update.

#### Zabbix

###### Zabbix Admin
Log in to Zabbix here http://`<ip address>`:30082.
1. On the top bar click "Administration" then "Users"
2. Click the _Admin_ user and on the next screen make any desired changes to the information.
3. Click the "Change Password" button in the middle of the screen to update the password
