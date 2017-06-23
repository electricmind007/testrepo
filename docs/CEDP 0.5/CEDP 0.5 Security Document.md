# Cognitive Enterprise Data Platform (CEDP) v0.5 Security Compliance

*Based on the [IT Security for Infrastructure and Application Design template](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/files/15161/infra.and.app.20160902.docx)*

Updated view of principles found here https://pages.github.ibm.com/it-standards/main/



Authors: Chief Data Office Governance and Data Engineering Teams

Reviewers: Michael Schimenti (CISO), Charles Hill (GCDO))

#1.0 Organization

#1.1 Scope and applicability

This standard establishes the principles and requirements for the protection of IBM world-wide information technology systems and the information they contain. The principles and requirements in this standard are defined by the Chief Information Officer (CIO) and the Chief Information Security Officer (CISO). The standard applies to all IBM operating units, corporate staffs and subsidiaries, without exceptions.

#1.2 Compliance

Compliance with this standard is mandatory, and is the responsibility of the operating units. Operating units should document any instances of non-compliance. Systemic deviations or alternative requirements can be brought forward by the Business Information Security Officer (BISO) of the operating unit for discussion with the CIO and CISO.

# 2.0 Data and Asset Classification and Protection

##2.1 Confidential Information

“Confidential Information” includes IBM Confidential and Client Confidential information. IBM Confidential information is defined in [LEG 116] (https://w3-03.ibm.com/ibm/documents/corpdocweb.nsf/ContentDocsByTitle/Corporate+Instruction+LEG+116/). 

CEDP 0.5 contains confidential information ad the following poicies are in place:

a) Access Control Policy in place to restrict access to Confidential Information to individuals on a need-to-know basis. Apply access controls as described in section 4. Access is controlled at the schema level.

b) Confidential Information encrypted in transit across any network (see section 5.1)

c) There is no removable media, including backup media

d) Media containing Confidential Information is properly disposed of (see section 8.2).

The following is a list of information requirements and their applicability. Any areas identified with confidential or sensitive information will be handled as required and documented in the appropriate sections of this document.

##2.2 Privacy Sensitive Information

PI information will be included in the 0.5 release of CEDP. See access control section for handling of access to sensitive information.

A complete list of datasets in 0.5 CEDP can be found [here (includes identification of PI data)] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/Worklist%20v3.xlsx)

An GPA assessment is under review on datasets being ingested into CEDP.  The assessment includes the types of information being ingested. The GPA review can be found [here] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/CEDP%202017%20Global%20Privacy%20Assessment.pdf)

The following is a summary of protections by class of data, and additional protections for regulated assets. The security requirements are based on the above data lists. 

Protection | Data contained in CEDP
-----------|---------------
Restrict access based on business need: | _Confidential and PI_
Explicit authz and re-authz processes  |  _NA_
Secure disposal of systems and media   |  _Confidential and PI_
Encrypt on the network              |     _Confidential and PI_
Encrypt on removable media           |    _NA_
Encrypt ‘at rest’ in app or db       |    _NA_
Log and monitor privileged access    |    _NA_
Log and monitor user access          |    _NA_
Identified and inventoried by BISO    |   _NA_



##2.3 E-Payment Data

This is not applicable to the CEDP 0.5 platform.

##2.4 Crown Jewel Data

There are no identified crown jewels to be included in 0.5 therefore this is not applicable to the CEDP 0.5 platform.

##2.5 Financially Significant Assets

Financial data incuded in MagicHat will not be part of 0.5, so there is no financially significant data stored in the CEDP 0.5.

No ASCA certification for 0.5. and therefore data can’t be used in any ASCA required processes.

# 3.0 Asset Management

## 3.1 Asset Inventory

CEDP is a registered asset in iRFP. The link to the asset record can be found [here](https://irfp-btit.sby.ibm.com:9443/fp/workspace/281/view/9605/element/317002)

A list of physical and virtual machines that make up the CEDP 0.5 production platform can be found in [CEDP 0.5 Physical and Virtual Inventory] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/CEDP%200.5%20Physical%20and%20Virtual%20inventory.md)

A list of application instances can be found [here] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/CEDP%200.5%20Application%20instances.md)

A list of application endpoints along with their respesctive owners and administrators can be found in [CEDP 0.5 Applications and endpoints] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/CEDP%200.5%20Applications%20and%20endpoints.md)

## 3.2 Acceptable Use

The assets currently in Zurich that are assigned for use to serve CEDP, which include compute nodes, storage nodes, and networking gear, are limited according to the following guidelines:

 * Only authorized IBM employees and contractors may use the CEDP.  No external individuals are to have access to any asset included within the CEDP.
 * Applications only for internal use by IBM business units are to be executing on these assets.
 * Confidential and personal information (PI) may be stored on CEDP assets.  
 * Sensitive personal information (SPI) and Crown Jewel data are **not** to be stored on any CEDP assets.

The procedures for obtaining access to data assets contained within the CEDP are summarized in the [Access Control Policy](#acl) section.

# 4.0 Access Control

## 4.1 Access Control Policy <a id="acl"></a>

In general:
Users must be authorized before accessing data
Users must request access to each individual source
User must provide proof of entitled access to original data source
Users cannot access sensitive data unless they have the credentials mentioned in previous bullet
First line managers must approve user access
CDO approves user access
Separation of Duties statement must exist for approvers
User must be part of a BlueGroup associated with a block of data. Different levels of access available: general, admin, author, view only
No access allowed to restricted data during blackout dates for any user
Control systems must be used for DB access
SAML provides user authentication
BlueGroup provides user authorization to the data lake
LDAP provide user authorization to the DB2 source
DB policy driven by LDAP for user access
Geography access integrity must be maintained

Initial users of CEDP 0.5 will be co-creators. These users will have access to a specified set of datasets identified by the co-creator project. Users accessing these datasets will have to request and be granted access by their first line manager, the CDO, and the project owner. The users must accept the terms that they will follow all IBM guidelines and policies before being allowed access to data in CEDP 0.5.

For the initial 0.5 release of the CEDP, a logical authorization matrix will determine which users can access which data sources in the data lake. This matrix will be implemented as LDAP groups. For each data source, there will be at least one  corresponding LDAP group. In order to read a data source, a user must request access for that source. Once approved, the user becomes a member of the data source's group(s). A user can be a member of multiple data source groups. Note that the approval process must include approval from the data source owner.

The data lake is composed of a variety of data technologies including Hadoop, DB2, BigSQL, and Elastic Search. Group-based authorization policies for across each technology will be deployed in a synchronized fashion. Policy deployment is realized with scripts internal to the data lake.

The bluegroups are maintained by the respective application owners (for example the bluegroup that lists all IGC administrators is maintained by the application owner of the IGC or their delegates).

A complete descrption of the access control process can be found [here] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/Data%20access%20governance%20v1.9.pdf)

## 4.2 User Access Management

Access management handled by original data source for access to any data for consumers. Consumers must be placed in a BlueGroup managed by the data owner or approved by the project owner in the case of a co-creator. BlueGroup synced with appropriate data soucre in CoEDL.

Access to CoEDL managed via BlueGroup. Request to BlueGroup supported through OneTEAM with required approvals. Allows access to personal schema and HDFS folder. No access to data (see above).

Co-creators allowed access to all data in CoEDL. Must sign and get approval for DOU prior to being allowed access. Addition to BlueGroup handled manually after DOU approval. 

### 4.2.1 User Identities

Each application is either using a bluepages backed login (so each user logs in using their IBM Intranet Password) or has integrated SSO with w3id.

System-level user accounts (on the hypervisors or virtual machines) are mapped via LDAP to bluepages identities and log in using SSH keys assisted with 2-factor authentication via Duo.

### 4.2.2 Application or System Identitiese

Application IDs (for example DB2's built-in user name) have non-expiring passwords but are never used for interactive
logins. For interactive (shell) access we will be authenticating against the production bluepages system using the IBM Intranet Password (IIP).

### 4.2.3 Access Provisioning

Each application owner must communicate the list of bluegroup names to the infrastructure team to be made available in the local LDAP.
The infrastructure team will also grant administrative access to the people listed as administrator for the application so they can set up the required group/role mappings for each bluegroup and/or user.

It is the application administrators' responsibility to make sure that the bluegroups and/or users are properly mapped.

User access to CoEDL requested via OneTeam
Approvals obtained
User mapped against BlueGroup 
Single use case: Report/Query data
Periodic sync of users in source to users in CEDP

Notes:  1 – Access granted at the source level. Must show they            already have access to original data source
             2 – Manual process in place as well where OneTEAM used
             3 – First line mgmt and CDO approval required
             4 – Single BlueGroup for all outbound data to allow into CoEDL, individual BlueGroups for each data source in CEDP


### 4.2.4 Managing Privileged Access Rights

NA per types of data being stored in CEDP 0.5


### 4.2.5 Review of Access Rights

Managed outside of CEDP and by original sources

### 4.2.6 Revocation of Access Rights

Managed outside of CEDP and by original sources


## 4.3 Application and System Access Control

### 4.3.1 Secure Logon Procedures

For all infrastructure level access (hypervisors, production virtual machines) SSH key based authentication together with **enforced** 2-factor authentication via CISO's Duo pilot project is required.

For all user-facing web services w3ID SSO authentication is recommended, however if not possible then can be substituted with an authentication mechanism that is using IBM Intranet Password from bluepages instead.

### 4.3.2 Password Management

Out of scope because all user facing services (including system accounts) are tied to bluepages identities and use the IBM Intranet Password, the w3ID application will enforce password policies.

### 4.3.3 Certificate Authentication

All HTTPS endpoints where users interact with any of the systems shall use a certificate signed by the internal IBM CA.
System-to-system endpoints can be permitted to use self-signed certificates.

The application environment was reviewed with the CISO organization on January 25, 2017. Issue raised about HDFS vulnerability. That issue is resolved and described in a section later in this document.

An overview of the application environment can be found [here] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/CEDP%20Primer%20-%20Jan%202017.pdf)

# 5.0 Use of Encryption

## 5.1 Data Encryption

Encrypted in transit between machines - Kerberos
Encrypted in transit via JDBC

At-rest encryption is currently **NOT** implemented at the Zurich Compute Cloud.

## 5.2 Key Management

No formal process in place for key management. There is a minimal number of requests and need so it is handled on a per case basis manually.

# 6.0 Operations Security

## 6.1 Documented Operating Procedures

This is currently work in progress as the lake is driven by ad-hoc requests at the moment by various teams all across the CDO. Once the dust settles
we will begin moving manual process to automated CI/CD piplines and for repeating requests come up with a template and a procedure on how to do it
(for example there is already a process description for getting additional compute resources next to the CEDP infrastructure).

## 6.2 Separation of Operational Environments

There is currently no dedicated and equivalently-setup test or dev infrastructure in Zurich.

## 6.3 Change Management

For every change request in the production infrastructure there needs to be a github ticket opened tagged with infrastructure that specifies the exact request.
Then a member of the infrasturcture squad will assess the request in terms of feasilibility and operational risks (impact to infrastructure) and can decide
if the change is required or not. If it has an impact then a maintenance window will need to be scheduled and all the changes need to be implemented there.
The requester *must* be available during the maintenance window to confirm if the change is accepted or not in order to avoid rolling the issue into the next
maintenance window.

## 6.4 Malware Protection

This is out of scope as all the systems are running Linux variants.

## 6.5 Data Backup

This will not be applicable for CEDP 0.5 as we will not be the original repository of source data.

## 6.6 Logging and Monitoring

All system and application log is streamed to a remote log server with at least a 90-day retention policy. 


## 6.7 Technical Vulnerability Management, Vulnerability Scanning, Remediating and Patching

Each physical and virtual system is registered into the Mixed Address Database which performs automated vulnerability scanning. This _could_ be supplemented with local vulnerability scanning however it is not planned at the moment.

For the physical hypervisor it is the infrastructure squad's responsibility to perform vulnerability remediation in a timely manner.
For each virtual machine it is the virtual machine owner's responbility to perform vulnerability remediation in a timely manner.

There is a specific vulnerbility for HDFS. CEDP 0.5 is a  fully kerberized HDFS meeting the requirements described [here]
(https://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/SecureMode.html)



# 7.0 Network Security

The whole infrastructure is in the blue zone.

The physical hypervisors will only be reachable from dedicated jumphosts. The infrastructure squad is the only team permitted access there.

Each virtual machine has security groups associated with them which perform the L3 filtering based on IP and port information.
For a detailed explanation [see this document](http://docs.openstack.org/user-guide/cli-nova-configure-access-security-for-instances.html)

The network and system was reviewed with the CISO team on January 27, 2017. No issues resulting from conversation.

# 8.0 Physical and Environmental Security

Description of the physical environment can be found [here] (https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/blob/master/documentation/CEDP%200.5%20Physical%20and%20Virtual%20inventory.md)

Zurich lab follows and is compliant with the guidelines located [here] (http://w3-03.ibm.com/transform/sas/as-web.nsf/ContentDocsByTitle/ITCS104+-+Physical+Access+Controls) for physical security.

Phsyical environment reviewed and observed January 19, 2017.

## 8.1 Controlled Access Assets

All physical infrastructure is in a controller area located on-premise at the IBM Research - Zurich Research Lab in Zurich, Switzerland.

Environment is a G1 controlled area and meets requirements as such. Access to room badged controlled and monitored. 

## 8.2 Secure Asset Disposal and Reuse

Faulty DASD logged and stored in G1 controlled room until destruction of devices. IBM employee acompanies drives to and monitors their destruction.

# 9.0 Supplier Management

NA

## 9.1 Supplier Engagement Requirements

NA

# 10.0 Security Incidents

The following incidents are to be managed

Suspected or confirmed penetration of CEDP
Hacked or compromised CEDP
Unauthorized access to CEDP
Suspicious IT activity of CEDP
Loss or compromise of IBM intellectual property
Loss or compromise of client information

## Incident Management

Incident management follows IBM procedures. The URL for incu=idet management is here http://w3-03.ibm.com/security/secweb.nsf/ContentDocsByCtryTitle/Europe~Incident+reporting

When an incident is identified, the reporting path below is used.

## Incident Reporting

Zurich lab to contact
Country 	Internal Number 	External Number 	International Number 
Switzerland 	3413 5000 	   +41 58 333 50 00   +44 2392 289868

