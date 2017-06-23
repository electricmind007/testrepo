# Slack alerts in Grafana


Grafana can send alerts to slack channels with some quick configuration.

##### SSL Cert
To communicate with the Slack server we first must provide the the Signing Authortiy certificate to the Grafana container before it starts (or a restart is required after the cert has been placed in the correct location). Without the certificate the communitcation will fail because grafana not trusted the Slack Cert.

The root CA cert is provided by GeoTrust and can be located here. [GeoTrust_Global_CA.pem](https://www.geotrust.com/resources/root_certificates/certificates/GeoTrust_Global_CA.pem)

Place the certificate here /data/cedp/grafana/cert/


##### Incoming webhooks
Incoming webhooks must be setup to enable external applications to communicate to specific channels.

1. Login to [Slack Apps](https://chiefdataoffice.slack.com/apps) here.
2. Search for "Incoming webhooks" in the search bar.
3. On the next screen click "Add Configuration"
4. Choose the channel you which to post messages to and click "Add Incoming Webhook integration"

On the next screen you will see a generate URL which you will use to communicate to the chosen slack channel

For example:
   ``https://hooks.slack.com/services/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``

##### Grafana coniguration

In the Grafana dashboard click the Icon in the top left and choose "Alerting" -> "Notification channels" from the drop down.

On the next screen fill in the details for the connector.

 * Type = Slack
 * URL = Is the url generated in the previous step. Ex. https://hooks.slack.com/services/T2FBNKG2X/B5Q5VBAF6/ekmJvlJBcHyWPeVQ4pOXyFz
 * Recipient = The channel chosen for the webhook. Ex. #my_webhook_test

##### More info
For more information on configuring alerys please see the Grafana documentation
http://docs.grafana.org/alerting/notifications/