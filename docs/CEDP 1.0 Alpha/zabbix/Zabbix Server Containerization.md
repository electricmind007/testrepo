# Zabbix Server Containerzation

The Zabbix Server consits of two essintial parts. The database which the server stores data in and the server itself. We will initially be including two optional pieces the Zabbix Web UI and the Java proxy for client code to communicate to the server through.

## Docker container versions
	* mysql:5.7
	* zabbix/zabbix-server-mysql:ubuntu-3.2-latest
	* zabbix/zabbix-agent:ubuntu-3.2-latest
	* zabbix/zabbix-web-nginx-mysql:ubuntu-3.2-latest

## Ports
Two external ports are configured:
	* 80 for the Web UI
	* 10051 for the agents to connect to the zabbix server.

More will be enabled as needed.