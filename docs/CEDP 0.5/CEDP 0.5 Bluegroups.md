# CEDP 0.5 Bluegroups

| LDAP group            | Owner                            | Description         |
| --------------------- | ---------------------------------| ------------------- |
| coedl_data_users  | Grant Miller (millerg@us.ibm.com)| The group of all external users of the CEDP. This group provides basic access to the CEDP along with a sandbox consisting of a HDFS home directory and a schema within the BigSQL database instance. All external users of the CEDP must be a member of this group.   |
| coedl_data_dev |Grant Miller (millerg@us.ibm.com)|  Access to CoEDL for developers   |
| coedl_cocreator   | Grant Miller (millerg@us.ibm.com)| CEDP co-creators. Members of this group have special developer status and as such receive permission to view all data in the CEDP using BigSQL and HDFS. |
| coedl_catalog_authors | Stephen Cureton (cureton1@us.ibm.com) | IGC users who have authoring permissions.|
| coedl_catalog_admin  | Stephen Cureton (cureton1@us.ibm.com)  | Full administrative access to production IGC |
| coedl_catalog_imam_users  | Stephen Cureton (cureton1@us.ibm.com)  | Users who have technical metadata authoring permissions. |
| coedl_catalog_users  | Stephen Cureton (cureton1@us.ibm.com)  | IGC general users |
| coedl_catalog_users_test | Stephen Cureton (cureton1@us.ibm.com)  | IGC general users | IGC general users for testing |
| coedl_riskinsi_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for Risk Insights co-creator project dataset|
| coedl_riskinsi_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for Risk Insights co-creator project dataset|  
| coedl_MRMRDPZP_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for PLM dataset| 
| coedl_MRMRDPZP_producer |Grant Miller (millerg@us.ibm.com)|  Srop Zone for PLM dataset| 
| coedl_MPW_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for MPW dataset| 
| coedl_SRMDB_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for SRM dataset| 
| coedl_SRMDB_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for SRM dataset|
| coedl_LDW_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for LDW dataset|
| coedl_LDW_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for LDW dataset|
| coedl_SDFDM_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for SDFDM dataset| 
| coedl_SDFDM_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for SDFDM dataset|
| coedl_SAPJP_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for MAPICS dataset| 
| coedl_SAPJP_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for MAPICS dataset|
| coedl_DSW_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for DSW dataset| 
| coedl_DSW_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for DSW dataset|
| coedl_IDC_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for IDC dataset| 
| coedl_IDC_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for IDC dataset|
| coedl_SOFTLAYR_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for Softlayer dataset| 
| coedl_SOFTLYR_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for Softlayer dataset|
| coedl_CAPIQ_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for CAPIQ dataset| 
| coedl_CAPIQ_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for CAPIQ dataset|
| coedl_CRDC_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for RDC dataset|
| coedl_TMT_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for TMT dataset| 
| coedl_TMT_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for TMT dataset|
| coedl_SAPODS_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for SAP ODS dataset| 
| coedl_SAPODS_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for SAP ODS dataset|
| coedl_ISCIW_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for ISC IW dataset| 
| coedl_ISCIW_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for ISC IW dataset|
| coedl_DNBMASTR_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for Dun & Bradstreet dataset| 
| coedl_DNBMASTR_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for Dun & Bradstreet dataset|
| coedl_OMCS_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for OMCS dataset| 
| coedl_OMCS_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for OMCS dataset|
| coedl_SOAR_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for SOAR dataset| 
| coedl_SOAR_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for SOAR dataset|
| coedl_ENOVIA_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for ENOVIA dataset| 
| coedl_ENOVIA_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for ENOVIA dataset|
| coedl_RESILINC_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for RESILINC dataset| 
| coedl_RESILINC_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for RESILINC dataset|
| coedl_BMSIW_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for BMSIW dataset| 
| coedl_BMSIW_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for BMSIW dataset|
| coedl_DAW_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for DAW dataset| 
| coedl_DAW_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for DAW dataset|
| coedl_COREMETRC_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for Core Metrics dataset| 
| coedl_COREMETRC_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for Core Metics dataset|
| coedl_UNICA_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for Unica dataset| 
| coedl_UNICA_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for Unica dataset|
| coedl_RFA_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for RFA dataset| 
| coedl_RFA_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for RFA dataset|
| coedl_EACM_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for EACM dataset| 
| coedl_EACM_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for EACM dataset|
| coedl_SPDI_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for SPDI dataset| 
| coedl_SPDI_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for SPDI dataset|
| coedl_HGDATA_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for HGDATA dataset| 
| coedl_HGDATA_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for HGDATA dataset|
| coedl_SMS_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for SMS dataset| 
| coedl_SMS_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for SMS dataset|
| coedl_ISCIWSG_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for ISC IWSG dataset| 
| coedl_ISCIWSG_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for ISC IWSG dataset|
| coedl_qryprod_producer |Grant Miller (millerg@us.ibm.com)|  Drop Zone for ISC qryprod dataset| 
| coedl_qryprod_consumer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for ISC qryprod dataset|
| coedl_IGAC_producer |Grant Miller (millerg@us.ibm.com)|  Landing Zone for ISC qryprod dataset|
| coedl_MPW_dev |Grant Miller (millerg@us.ibm.com)|  Admin to Landing Zone for MPW dataset|
| coedl_ODM_producer |Grant Miller (millerg@us.ibm.com)|  Admin to Landing Zone for ODM dataset|
| coedl_MPW_consumer_public |Chris Giblin (cgi@zurich.ibm.com)|  Test group for row-level AC|
