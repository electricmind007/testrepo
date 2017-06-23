# Zabbix Agent Containerzation

The Zabbix Agent pod is a single docker container. Which communicated directly to the Zabbix server to report OS level metrics such as disk usage and cpu usages

## Docker container versions
	* zabbix/zabbix-agent:ubuntu-3.2-latest
	
## Ports
One external port is configured:
	* 10050 for the Zabbix server to communicate directly to the agent
   
## Before deploy
There is a limitation with the zabbix agent. Due to security the servers that are allowed to communicate with the agents must be known (by IP) before deployment. The zabbix agent does not allow for an IP mask and the host name is not know so we can't rely of name resolution. The only option is to modify the yaml script before deploying the daemonset with the following command

    SERVER_IP=`kubectl get pods -o wide | grep zabbix-server | \
        awk '{print $6}'` && \
        sed -i -e 's/$SERVER_IP/'"$SERVER_IP"'/g' daemonset-zabbix-agent.yml`
