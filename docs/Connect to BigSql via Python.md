# **Connect to BigSQL via Python (Windows 7)**  
1.  [Install DB2 Client and Catalog BIGSQL](https://github.ibm.com/cognitive-data-platform/caas-sample/blob/master/documentation/StructuredDataWEXtoSPSS/CreateCEDPODBCDataSource.md)   
    Follow the document till step 9.    
2.  [Install ibm_db library for Python](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Installing_Python_ibm_db_driver?lang=en)   
       
3.  Reference the catalogue in python. Sample working code  below :     
    
    #importing library   
    `import ibm_db`   
                  
    #creating connection to bigsql  
    `conn = ibm_db.pconnect('BIGSQL',username,password)`        

    #running queries    
    `sql = "select * from opendata.plants " `    
    
# Connect to BigSQL via Python (Ububtu 16.04)
 
1.	Use [IBM DOWNLOAD LINK](http://w3-103.ibm.com/software/xl/download/ticket.wss ) to download all IBM related software’s. You might get some certificate issue. To resolve the issue set an exception for the site.
2.	Download IBM Data Server Client V11.1 for Linux on AMD64 and Intel EM64T systems (x64) Multilingual (**CNB0NML**) using the link mentioned in Sl.1. Use the **part number** to search the product.
3.	Unzip the downloaded software & then use the command, sudo ./db2_install, from the directory where you have placed the unzipped DB2 Client.
4.	Download IBM® DB2® Support Files for SSL Functionality 11.1 for Linux® on AMD64 and Intel® EM64T systems (x64) (**CNB10ML**) using link 1
5.	Unzip the downloaded SSL Functionality  
6.	Run following command from the directory where you have placed your unzipped SSL functionality  
  * sudo apt-get install alien dpkg-dev debhelper build-essential
  * Run ls. You will see 2 files 
    *	gskcrypt64-8.0.50.57.linux.x86_64.rpm   
    * gskssl64-8.0.50.57.linux.x86_64.rpm
 *  Run sudo alien gskcrypt64-8.0.50.57.linux.x86_64.rpm gskssl64-8.0.50.57.linux.x86_64.rpm 
 *  Run ls again in same directory. You will see two more files
    *	gskcrypt64_8.0-51.57_amd64.deb  
    * gskssl64_8.0-51.57_amd64.deb
 * Run sudo dpkg -i gskcrypt64_8.0-51.57_amd64.deb in same directory
 * Run sudo dpkg -i gskssl64_8.0-51.57_amd64.deb in same directory.
  * Go to root directory & run sudo find -name "gsk8capicmd*" to locate gsk8capicmd
7.	For my case it is /usr/local/ibm/gsk8_64/bin
8.	Create keystore directory in your home directory mkdir ~/GSK8Store 
9.	Download carootcert.der, caintermediatecert.der from  [here](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/wiki/TLS-to-BIGSQL) ( Under GET IBM Internal Root CA section)
10.	Place the 2 “.der” files under GSK8Store directory
11.	Go to the GSK8Store directory.
12.	Run following commands
 * (full path to your gsk8capicmd_64) /gsk8capicmd_64 -keydb -create -db "keyclient.kdb" -pw "vLYpWnJoi27ir7ZM" -stash
 * (full path to your gsk8capicmd_64)/gsk8capicmd_64 -cert -add -db "keyclient.kdb" -pw "vLYpWnJoi27ir7ZM" -label "carootcert" -file carootcert.der -format binary -fips
 * (full path to your gsk8capicmd_64)/gsk8capicmd_64 -cert -add -db "keyclient.kdb" -pw "vLYpWnJoi27ir7ZM" -label "caintercert" -file caintermediatecert.der -format binary -fips
 * db2 update dbm cfg using SSL_CLNT_STASH (full path to your home) GSK8Store/keyclient.sth
 * db2 update dbm cfg using SSL_CLNT_KEYDB (full path to your home) GSK8Store/keyclient.kdb
•	db2 connect to BIGSQL user <USERNAME> using <PASSWORD>
(USERNAME is your w3id. Replace @ and . in your w3id with _)
13.	While running the gsk8capicmd_64  if you get an error message that  some file is missing then go to your root directory & search using find command the location of that missing file .
14.	Create a .config file(with any name) under etc/ld.so.conf.d/ & update the path of missing file in that .config file
15.	After that run sudo ldconfig.
16.	Go to your home directory & run pip install ibm_db
17.	Open spyder editor & then run following codes 
``` import ibm_db_dbi as dbi
import pandas as pd   

    sql = "select * from opendata.plants limit 10"
    con1=dbi.connect('BIGSQL','your w3id. Replace @ and . in your w3id with _ ','w3 password') 
    df=pd.read_sql(sql,con1) 


    
