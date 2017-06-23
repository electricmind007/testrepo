# CEDP 0.5 Physical and Virtual inventory

## Total allocated resources: 1823/1008 vCPUs, 10900/9216 GB

## Hypervisors:

| Hostname            | IP Address  | CPU | Mem    | OS         |
| ------------------- | ----------- | ---:| ------:| ---------- |
| c04n2.i.zc2.ibm.com | 9.4.127.126 | 56  | 512 GB | RedHat 7.3 |
| c04n3.i.zc2.ibm.com | 9.4.127.127 | 56  | 512 GB | RedHat 7.3 |
| c04n4.i.zc2.ibm.com | 9.4.127.128 | 56  | 512 GB | RedHat 7.3 |
| c07n1.i.zc2.ibm.com | 9.4.127.149 | 56  | 512 GB | RedHat 7.3 |
| c07n2.i.zc2.ibm.com | 9.4.127.150 | 56  | 512 GB | RedHat 7.3 |
| c07n3.i.zc2.ibm.com | 9.4.127.151 | 56  | 512 GB | RedHat 7.3 |
| c07n4.i.zc2.ibm.com | 9.4.127.152 | 56  | 512 GB | RedHat 7.3 |
| c07n5.i.zc2.ibm.com | 9.4.127.153 | 56  | 512 GB | RedHat 7.3 |
| c07n6.i.zc2.ibm.com | 9.4.127.154 | 56  | 512 GB | RedHat 7.3 |
| c07n7.i.zc2.ibm.com | 9.4.127.155 | 56  | 512 GB | RedHat 7.3 |
| c07n8.i.zc2.ibm.com | 9.4.127.156 | 56  | 512 GB | RedHat 7.3 |
| c08n1.i.zc2.ibm.com | 9.4.127.165 | 56  | 512 GB | RedHat 7.3 |
| c08n2.i.zc2.ibm.com | 9.4.127.166 | 56  | 512 GB | RedHat 7.3 |
| c08n3.i.zc2.ibm.com | 9.4.127.167 | 56  | 512 GB | RedHat 7.3 |
| c08n5.i.zc2.ibm.com | 9.4.127.169 | 56  | 512 GB | RedHat 7.3 |
| c08n6.i.zc2.ibm.com | 9.4.127.170 | 56  | 512 GB | RedHat 7.3 |
| c08n7.i.zc2.ibm.com | 9.4.127.171 | 56  | 512 GB | RedHat 7.3 |
| c08n8.i.zc2.ibm.com | 9.4.127.172 | 56  | 512 GB | RedHat 7.3 |
| **Total**           |             | **1008** | **9216 GB** |  |

## Physical servers:

| Hostname            | IP Address  | CPU | Mem    | Disk     | OS         |
| ------------------- | ----------- | ---:| ------:| -------- | ---------- |
| c04n1 | 9.4.127.125 | 56  | 512 GB | 2x128 GB SSD (system), 5 TB Flash (BigSQL temp) | RedHat 7.3 |
| c13n1 | 9.4.127.157 | 8   | 64 GB  | 2x480 GB SSD (system) | RedHat 7.3 |
| c13n2 | 9.4.127.158 | 8   | 64 GB  | 2x480 GB SSD (system) | RedHat 7.3 |
| c13n3 | 9.4.127.159 | 8   | 64 GB  | 2x480 GB SSD (system) | RedHat 7.3 |
| c13n4 | 9.4.127.160 | 8   | 64 GB  | 2x480 GB SSD (system) | RedHat 7.3 |
| c13n5 | 9.4.127.161 | 8   | 64 GB  | 2x480 GB SSD (system) | RedHat 7.3 |
| c13n6 | 9.4.127.162 | 8   | 64 GB  | 2x480 GB SSD (system) | RedHat 7.3 |
| **Total** |         | **104** | **896 GB** | **2x3008 GB SSD, 5 TB Flash** | |

## Virtual instances used by the core infrastructure (datalake-infrastructure):

| Hostname                  | IP Address  | CPU | Mem    | Disk  | OS    |
| ------------------------- | ----------- | ---:| ------:| ----- | ----- |
| auth-1       | 9.4.135.140 | 2   | 8 GB   | 15 GB | RedHat 7.3 |
| auth-2       | 9.4.135.141 | 2   | 8 GB   | 15 GB | RedHat 7.3 |
| bi-guest-1   | 9.4.137.148 | 8   | 64 GB  | 50 GB (system), 1 TB | RedHat 7.3 |
| bi-lb-1      | 9.4.136.52  | 8   | 64 GB  | 15 GB | RedHat 7.3 |
| bi-lb-2      | 9.4.136.54  | 8   | 64 GB  | 15 GB | RedHat 7.3 |
| bi-worker-1  | 9.4.136.152 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-2  | 9.4.136.153 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-3  | 9.4.136.155 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-4  | 9.4.136.154 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-5  | 9.4.136.159 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-6  | 9.4.136.162 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-7  | 9.4.136.166 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-8  | 9.4.136.160 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-9  | 9.4.136.16  | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-10 | 9.4.136.161 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-11 | 9.4.137.206 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-12 | 9.4.137.208 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-13 | 9.4.137.21  | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-14 | 9.4.137.210 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-15 | 9.4.137.211 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-16 | 9.4.137.212 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-17 | 9.4.137.213 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-18 | 9.4.137.214 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| bi-worker-19 | 9.4.137.216 | 28  | 240 GB | 50 GB (system), 13 TB (DataNode) | RedHat 7.3 |
| catalog-db2-hadr-1   | 9.4.135.56 | 28 | 240 GB | 50 GB (system), 5 TB (DB2) | RedHat 6.7 |
| catalog-db2-hadr-2   | 9.4.135.55 | 28 | 240 GB | 50 GB (system), 5 TB (DB2) | RedHat 6.7 |
| igc-1                | 9.4.135.41 | 8 | 64 GB | 50 GB | RedHat 7.3 |
| igc-2                | 9.4.135.42 | 8 | 64 GB | 50 GB | RedHat 7.3 |
| igc-quorum           | 9.4.135.62 | 1 | 1 GB | 15 GB | RedHat 7.3 |
| iis-igc-glusterfs    | 9.4.135.235 | 8 | 64 GB | 50 GB | RedHat 7.3 |
| iis-igc-nfs-1        | 9.4.135.230 | 1 | 1 GB | 50 GB | RedHat 7.3 |
| integration-db2-1    | 9.4.135.196 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| integration-db2-2    | 9.4.135.198 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| integration-db2-3    | 9.4.135.38 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| integration-db2-4    | 9.4.135.199 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| integration-db2-5    | 9.4.135.2 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| landing-egress-db2-1 | 9.4.135.201 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| landing-egress-db2-2 | 9.4.135.200 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| landing-egress-db2-3 | 9.4.135.205 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| landing-egress-db2-4 | 9.4.135.204 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| landing-egress-db2-5 | 9.4.135.203 | 28 | 240 GB | 50 GB (system), 50 TB (shared, DB2) | RedHat 7.3 |
| additional VMs for infrastructure testing | N/A  | 29 | 26 GB | 200 GB (system) | RedHat 7.3 |
| **Total**            |   | **951** | **7868** GB | **260 TB, 50 TB (shared)** | |

For all virtual machines the suffix is `.datalake-infrastructure.zc2.ibm.com` and for all physical machines the suffix is `.i.zc2.ibm.com`

## Additional project allocations:

| Project          | Instances | vCPU | Mem     | Disk   |
| ---------------- | ---------:| ----:| -------:| ------:|
| datalake-test    | 25        | 50   | 100 GB  | 4,0 TB |
| datapipeline     | 96        | 304  | 1472 GB | 170 TB |
| CompanyServices  | 10        | 40   | 200 GB  | 1,5 TB |
| catalogui-lb-iis | 10        | 8    | 64 GB   | 0,5 TB |
| clp-iis          | 10        | 8    | 8 GB    | 0,5 TB |
| DNBMASTR         | 10        | 8    | 16 GB   | 100 GB |
| iis-prod-cedp    | 10        | 114  | 322 GB  | 4,0 TB |
| [Brad Debroni](http://w3.ibm.com/newbp/profile.html?uid=077291649)| 10 | 20 | 50 GB | 1,0 TB |
| [Chris Lamb](http://w3.ibm.com/newbp/profile.html?uid=077308649)| 10 | 20 | 50 GB | 1,0 TB |
| [Jared Wong](http://w3.ibm.com/newbp/profile.html?uid=077340649)| 10 | 20 | 50 GB | 1,0 TB |
| [Maranatha Luckanachai](http://w3.ibm.com/newbp/profile.html?uid=0A0877649)| 10 | 20 | 50 GB | 1,0 TB |
| [Matthew Hoener](http://w3.ibm.com/newbp/profile.html?uid=0D0097649)| 10 | 20 | 50 GB | 1,0 TB |
| [Mike McDonald](http://w3.ibm.com/newbp/profile.html?uid=078781649)| 10 | 20 | 50 GB | 1,0 TB |
| [Rick McCall](http://w3.ibm.com/newbp/profile.html?uid=085585649)| 10 | 20 | 50 GB | 1,0 TB |
| [Rupinder Parhar](http://w3.ibm.com/newbp/profile.html?uid=014716649)| 10 | 20 | 50 GB | 1,0 TB |
| [Victor Dhami](http://w3.ibm.com/newbp/profile.html?uid=077292649)| 10 | 20 | 50 GB | 1,0 TB |
| [Wei Zhang](http://w3.ibm.com/newbp/profile.html?uid=078100649)| 10 | 20 | 50 GB | 1,0 TB |
| [Chaitali Gupta](http://w3.ibm.com/newbp/profile.html?uid=0J2439897)| 10 | 20 | 50 GB | 1,0 TB |
| [Randy Weinstein](http://w3.ibm.com/newbp/profile.html?uid=0J0439897)| 10 | 20 | 50 GB | 1,0 TB |
| [Syed Ahmed](http://w3.ibm.com/newbp/profile.html?uid=8A6991897)| 10 | 20 | 50 GB | 1,0 TB |
| [Qinlong Luo](http://w3.ibm.com/newbp/profile.html?uid=0J1504897)| 10 | 20 | 50 GB | 1,0 TB |
| [Bryan Kyle](http://w3.ibm.com/newbp/profile.html?uid=082602649)| 10 | 20 | 50 GB | 1,0 TB |
| [Ahmed Abdel Monsef](http://w3.ibm.com/newbp/profile.html?uid=064322865)| 10 | 20 | 50 GB | 1,0 TB |
| [Neera Mathur](http://w3.ibm.com/newbp/profile.html?uid=520832897)| 10 | 20 | 50 GB | 1,0 TB |
| **Total**            |  **341** | **872** | **3032** GB | **197,6 TB** | |

