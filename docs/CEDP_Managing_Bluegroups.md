# Managing CEDP 0.5 Bluegroups

To add a new **Bluegroup** to the LDAP server in Zurich: extend the [cognitive-data-platform/production-infrastructure-zurich/bluepages_ldap_sync/ldapsync.conf](https://github.ibm.com/cognitive-data-platform/production-infrastructure-zurich/blob/master/bluepages_ldap_sync/ldapsync.conf) file!

1. Clone the **cognitive-data-platform/production-infrastructure-zurich** repository:
         
         git clone git@github.ibm.com:cognitive-data-platform/production-infrastructure-zurich.git
         
    Or [edit directly](https://github.ibm.com/cognitive-data-platform/production-infrastructure-zurich/blob/master/bluepages_ldap_sync/ldapsync.conf) the ldapsync.conf file.

2. Make your modifications and commit them to a **different branch than master !** (The master branch is protected.)
3. Create a pull request where you commited your changes!
   This will trigger Travis CI to build your pull request - you will have to wait for the build to finish before you are allowed to merge your modifications.
   Once the build gets the green light, Github will allow you to merge your PR to the master branch.

4. After the merge, a final build will be created from the master branch, this will deploy the configuration to the production environment. 
   On the environment there is a cronjob running every 10 minutes, to check for changes in the configuration file and to update the system accordingly.
   The whole process approximately takes 10-30 minutes before your changes can take effect.
   

In order to protect the live LDAP configuration only adding new lines is allowed.
DO NOT change any EXISTING lines in the configuration file!
Otherwise your pull request will fail - the automation only supports adding new groups. If you would like to remove any of the existing entries, add a comment for the @infrastructure-team to your PR, so one of the administrators can manually approve it.

The following actions will not fail your build:
 - adding new lines
 - rearranging the existing lines
 - removing empty lines
 
NOTE: This is a temporary solution an API is under way for replacement.

PR: stands for pull request for short.
