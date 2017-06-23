# CaaS-Sample Application

The sample [Loan and Approval Sample Application](https://github.ibm.com/cognitive-data-platform/caas-sample#welcome-to-the-caas-sample) demonstrates how all of the pieces within CaaS v0.5 fit together. It starts with a collection of existing 'Loan Application' PDF files containing loan approval information for a variety of customers which will use systemT within WEX to annotate the useful data. Then data is ingested from a CEDP database and read into a WEX collection. Having acquired all this data, export it all into SPSS and train a model on what represents a good loan risk. Using BlueMix deploy this model and create an endpoint to expose the training. Finally, wrap all of these pieces into one Application hosted in WEX's AppBuilder.

  ![overview.png](https://github.ibm.com/cognitive-data-platform/caas-sample/blob/master/documentation/overview.png)
