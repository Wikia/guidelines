**CAUTION!** This document refers to Wikia internal structures, groups, processes, and procedures that may not make sense in isolation, or which may be misleading without the appropriate Wikia organizational context.

If you'd like to learn more or become a volunteer developer for Wikia, please check out our [Volunteer Developer program](http://dev.wikia.com/wiki/Volunteer_Developers) for more details.

---
# Introduction

This document describes the programming languages currently used and supported at 
Wikia, and the criteria for adopting a new programming language.

At Wikia we value choosing the right tool for the job, but we also recognise that
every language we adopt comes with a cost. As such, we have chosen  a set of default 
languages that cover most common areas of development, while providing a framework 
for adopting new languages as necessary.

Any questions about this process should go to the lead architect.

## Default Programming Languages

Teams should always prefer using the default language appropriate to their project's 
domain. These languages are the best understood and supported across the company, 
and provide the most scope for re-using other teams code, or contributing code that 
can be reused in other projects.

The currently supported general purpose languages are:

 * PHP - for projects related to existing PHP codebases
 * Java - for services, Android development and automated testing (Selenium)
 * Javascript - for front-end development
 * Python - for scripting and tools development, and internal services 
            that do not provide user-facing functionality
 * Objective-C - for iOS development

### Criteria for Introducing a Default Programming Language

To introduce a new default language:

 * The language must fulfil a clear need that spans multiple projects or teams 
    that is not covered by an existing default, or
 * The language must be intended to replace an existing default

Otherwise the criteria are the same as introducing a new special-purpose language
below, except with a higher bar for acceptance.

There is significant cost to adding a default language to the environment due to the 
training, tooling, and operational support that will be required to support the language 
in production. As such the number of general purpose languages should remain small.

## Special Purpose Programming Languages

A project can use a language other than the default if:

* the project is self-contained and will be limited to a single team.
* the language is specifically suited to that project due to its language/runtime,
  available libraries, or available reusable code.

Any decision to use a non-default language for a project should be reviewed by the
architect. Preference will be given to languages that have already been used
successfully in Wikia.

The following is an incomplete snapshot of non-default languages deployed in Wikia:

| Language    | Used in…                                          | Status              |
|-------------|---------------------------------------------------|---------------------|
| Clojure     | vignette (thumbnailer), poky and casino (Huddler) | sunset              |
| Javascript* | Parsoid, Mercury server-side components           | actively developed  |
| Typescript  | Ad Engine, Silver Surfer.                         | actively developed  |
| Ruby        | Chef scripting, analytics UI                      | actively developed  |
| Go          | Helios (auth  server)                             | baing phased out    |
| Scala       | Phalanx                                           | sunset.             |
| Ruby        | Analytics UI (Data Eng)                           | being phased out    |
| Perl        | Various legacy scripts                            | maintenance only    |


(*) Default language being used outside its domain

### Considerations for Adopting a New Programming Language

To introduce a new language into production at Wikia, the following questions need to be 
answered:

1. How do the advantages of adopting this language outweigh the burden of supporting it?
2. How are we going to enable developers to write high quality, maintainable code
   in this language?
3. How are we going to deploy services written in this language to production, and
   keep them running smoothly against Wikia levels of traffic?
4. How do we intend to mitigate the risk that the language does not meet our needs,
   and will have to be removed or replaced?
5. If this language is intended to replace another language in use at Wikia, what
   is our schedule to phase the old out in favour of the new?

Some questions that will help us answer the above:

 1. Are there significant advantages (or potential advantages) to using the proposed
     language? This question includes libraries and open source projects that allow
     us to not re-implement existing work.
 2. Is there any specialist knowledge required to run the language in production, for
    example VM or Garbage Collection tuning?
 3. Is there existing organizational expertise in the proposed language?
 4. Do we have previous experience developing with the language at Wikia? What was
   the outcome?
 5. What evidence exists to support this language as a viable choice? Some metrics
    that might be used to answer this question are:
  1. Questions on stack overflow.
  2. Mailing list activity.
  3. Production deployments at a similar traffic volume.
  4. Job openings in the industry specifically targeting this language.
 6. Does the language affect the organization’s ability to recruit?
  1. How likely is it that a generalist developer will have some experience with the language?
  1. Is there a large enough pool of developers that we can recruit for it specifically?
  2. Does the presence of the language make Wikia more attractive to developers?
 7. How much training is available either internally or externally?
 8. Are there textbooks and reference materials for the proposed language?
 9. Is there a pool of developers that could be hired to support and expand the development of services
    written in the language?
 10. Is the language intended for a small, standalone project, or is there a small standalone project
     that can serve as a pilot for adopting the language more generally?
 11. Is the expected learning curve for becoming productive in the language
    reasonable given the scope in which it is going to be used?
 12. If the project and language combination does not succeed, is
     there room in the timeline to rewrite it in another language?
