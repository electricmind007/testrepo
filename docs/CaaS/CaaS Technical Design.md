# Cognition as a Service Technical Design

## Overview

The Cognitive Enterprise Data Platform (CEDP) will have a natural language question and answer interface that allows consumers of the CEDP to enter a question in natural language and be directed to an answer.  In IBM Design Thinking terms, the main hill for CaaS is as follows:

  > Enable business users of the Cognitive Enterprise Data Platform to enter natural language queries and be directed to an answer or set of answers.

  * Who: Business users
  * What: Directed to answers
  * Wow: Natural language queries

This document outlines the core components of this interface and how they relate to one another.

## High Level Design

At a high level, the CaaS features are broken down into several main components.  Each of these components are implemented as separate microservices that are deployed (currently) as a single pod on Kubernetes.  Conceptually, the architecture is such that a natural language input is received from a user interface.  The intent is derived from the natural language input and the appropriate analytic is called that matches the intent.  In order for the system to know what intents it's capable of taking action on, each analytic will register itself along with its intents and other metadata in a catalog.

### User Interface

The interface through which a business user interacts with the CaaS NLQA system is a simple Web UI.  Presently the technical design of the UI is a simple 3-tiered application with a Web front end and a Node.js backend which communicates with the various Thrift services that it needs to talk to.  This approach means that the UI can be implemented quickly but it also means that the logic is locked into a REST API implemented in Node.js.  Once the interface is better understood the backend of the user interface will be replaced with the logic moving into a Thrift service and a thin REST API wrapper around the Thrift service.  Having a thrift service backend will make the UI more like other backend components and will facilitate the implementation of a CLI to interact with it.  Moreover, because the logic will be in Thrift, a secondary front end can be developed that allows interaction by way of a Slack bot.

Detailed technical documentation related to the [User Interface](User Interface.md) is available.


### Intent Router

The intent router is a critical component to the technical design of this application.  It's responsible for taking in the query from the user and directing them to the appropriate analytic.  As such most of the brains of the application is within the intent router as it is responsible for deriving intent and performing a shallow analysis of the input query.

The current plan is that the intent router will be backed by a Natural Language Classifier that has been trained on a corpus of sample questions from the analytics.  When a question is given to the intent router, the question will be forwarded on to the trained NLC and an intent along with a confidence score is returned.  Based on the intent and confidence score the intent router will return the question analysis data along with the top ranked intents and confidence scores to the caller.  Returning the information to a calling service allows the calling service to determine what is the appropriate action to take.  Some examples of appropriate action include:

  * Calling the analytic and returning the result(s).
  * Based on low confidence scores across all of the returned intents, indicate to the user that the system cannot understand their intent.
  * Based on a tight clustering of confidence scores across the returned intents, allow the user to disambiguate their intent.

Detailed technical documentation related to the [Intent Router](Intent Router.md) is available.


### Analytic Module Catalog

The intent router needs to understand what intents are available to route to.  However the job of managing the catalog of analytics and their associated intents should not belong to the intent router.  Rather this job falls on a separate service that understands the lifecycle of a module from registration, through deployment, and eventually to unregistration and undeployment.

Detailed technical information about the [Analytic Module Catalog](Analytic Module Catalog.md) is available.

### Command Line Interface

A component of the CEDP is some sort of command line interface that allows a user to interact with the platform.  This interface should expose the functions of the platform so that the platform can be scripted and used from the command line without having to write custom code to interact with services and generate binary data structures.  The command line interface is a bundle of Python code that can be extracted into a single directory and called directly.  As new features are added to the platform, the CLI will be updated to accomodate these features. Usage help for the CLI can be obtained by running it with the `--help` option:

    $ cli/bin/cedp --help

See the [Command Line Interface](Command Line Interface.md) for more detailed information about the tool.

# Security

TBD
