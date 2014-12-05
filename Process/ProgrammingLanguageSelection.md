# Introduction

This document describes the currently supported programming languages and the
criteria for selecting a new programming language. Programming languages at
Wikia fall into two categories-- general purpose and specialized.

## General Purpose Programming Language Criteria

To introduce a new general purpose language the following criteria needs to be
met in addition to the specific language criteria:

 1. Anyone in engineering can move to a project using the language with minimal
    effort and be productive.
 2. There is a large pool of developers to recruit from in all geographies where
    Wikia employs engineers.

There is significant cost to adding a general purpose service language to the
environment due to the training, tooling, and operational support that will be
required to support the language in production. As such the number of general
purpose languages should remain small.

The currently supported general purpose languages are:

 * PHP
 * Python
 * Java (proposed) - for general purpose service development.

These general purpose languages are no longer supported:

 * Perl

PHP is included because of the organizational expertise and investment in
MediaWiki and the Huddler code base. Python was previously vetted by the
architect and service development using the language has begun (deploy-tools,
monetization, and data engineering).

## Specialized Programming Language Criteria

To introduce a new specialized language into production at Wikia, the following
questions need to be answered:

 1. Are there significant advantages (or potential advantages) to using the proposed
     language? This question includes libraries and open source projects that allow
     us to not re-implement existing work.
 2. How will the programming language be deployed and run in production?
 3. What evidence exists to support this language as a viable choice? Some metrics
    that might be used to answer this question are:
  1. Questions on stack overflow.
  2. Mailing list activity.
  3. Production deployments at a similar traffic volume.
  4. Job openings in the industry specifically targeting this language.
 4. Is there existing organizational expertise in the proposed language?
 5. Does the language affect the organization’s ability to recruit?
 6. How much training is available either internally or externally?
 7. Are there textbooks and reference materials for the proposed language?
 8. Is there a pool of developers in in all geographies where Wikia employs
    engineers that could be hired to support and expand the development of services
    written in the language?
 9. Is the expected learning curve for becoming productive in the language
    reasonable given the scope of the project?
 10. If the project and language combination does not succeed (e.g. the only
     developer leaves, many production failures, difficulty in debugging errors) is
     there room in the project timeline to rewrite it in another language?
 11. The scope of the project being considered is self-contained and limited to a few
     (2-3) developers

The currently supported special purpose languages are:

 * Clojure - for the thumbnailer (vignette), poky (Huddler), and casino (Huddler)
 * Node.js - for parsoid
 * Typescript - for Mercury front-end and intermediary code
 * Ruby - for chef and the analytics UI
 * Go (undergoing evaluation with the authentication project)

These special purpose languages are still in use but no longer supported:

 * Scala

Questions regarding the introduction of a new language as well as the request to
introduce a new language should be sent to the [architecture
committee](https://one.wikia-inc.com/wiki/Engineering/Architecture_Committee).
