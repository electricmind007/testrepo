## Setting up test automation environment

To set up test automation environemnt, run ansible playbook [test.yml](../../ansible/playbooks/test/test.yml) which will instead all the required software. This includes
- [Protractor](http://www.protractortest.org): this is the end-to-end test framework all automated CaaS UI test cases are based on
- [Node Version Manager](https://github.com/creationix/nvm), aka: NVM. This is used to manage multiple installs of NodeJS.
- [NodeJS](https://nodejs.org/en/): prerequisite to running Protractor. Two versions of NodeJS are installed due to requirement difference between test automation and CaaS, therefore need for NVM
- [PhantomJS](http://phantomjs.org/): needed for headless testing

## Executing test cases

To execute existing test cases, run shell script [runTests.sh](../../test-cases/CaaS-UI/runTests.sh).
This script will remove and update webdriver, startup a local copy of selenium driver and execute all test cases stored under [test-cases/CaaS-UI/specs](../../test-cases/CaaS-UI/specs) directory

## Adding new test cases

New test cases should be added to diretory [test-cases/CaaS-UI/specs](../../test-cases/CaaS-UI/specs) where the naming convention is `<test-case-name>-spec.js`
