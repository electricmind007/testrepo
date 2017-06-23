# Standards and Conventions

This document outlines standards and conventions for the CaaS Platform to ensure that all of the components work together as painlessly as possible.

## Thrift Standards

This section outlines the standards that are in place for the usage of Thrift throughout the CaaS platform.  These standards apply to components of the platform itself as well as extension modules written by co-creators.  Where possible these standards have been codified so as to remove the need for reviewing documentation.

### Protocol and Transport

We standardize on using the `TCompactProtocol` for all interaction between services over a `TServerSocket` connection.  Standardizing on these means that various components can work together without having to understand how to talk to one another.
