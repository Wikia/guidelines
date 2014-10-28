# Introduction

This document describes the currently supported programming languages and the
criteria for selecting a new programming language.

## Programming Language Selection

The following programming languages are supported for service development in
production at Wikia.

 * PHP
 * Python
 * JavaScript (including node.js applications)
 * Clojure
 * Java

By choosing one of these languages implementors leverage existing organizational
experience in productizing services in these languages as well as the production
tooling (deploy tools, logging, and monitoring).

If a language other than one of those listed above will be considered, please
see the new programming language section below for the process for selecting a
new programming language.

Note that there are legacy systems in production using programming languages
listed above. There are existing systems implemented using:

 * Ruby
 * Perl
 * Scala

Although there is some production support for these languages they are
considered legacy and should not be selected for future development unless they
go through the process described below.

## New Programming Language

This section describes the process that should be followed when introducing a
new language into the Wikia production environment.

To introduce a new language into production at Wikia, the following questions
need to be answered:

 1. What does the proposed language offer that is not provided by one of the
		currently supported languages?
 2. How will the programming language be deployed and run in production?
 3. How mature is the industry support for the language? Some metrics that might
		be used to answer this question are: questions on stack overflow, mailing
		list activity, production deployments at a similar traffic valume.
 4. Is there existing organizational expertise in the proposed language?
 5. Does the language effect the organization’s ability to recruit?
 6. What is the expected learning curve for becoming productive in the language?
 7. Is there a pool of developers in both Poland and the US that could be hired
		to support and expand the development of services written in the language?

In addition to the questions above, the developers seeking to use a new language
should consider the impact to the business in selecting a new language for the
project beginning development. For example, does the effort needed to
introduce and learn a new language put the expected delivery of the required
functionality at risk?

Questions regarding the introduction of a new language as well as the request to
introduce a new language should be sent to the [architecture
committee](https://one.wikia-inc.com/wiki/Engineering/Architecture_Committee).
