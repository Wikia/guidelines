**CAUTION!** This document refers to Wikia internal structures, groups, processes, and procedures that may not make sense in isolation, or which may be misleading without the appropriate Wikia organizational context.

If you'd like to learn more or become a volunteer developer for Wikia, please check out our [Volunteer Developer program](http://dev.wikia.com/wiki/Volunteer_Developers) for more details.

---
# Pull Request Guidelines

## Background

Github pull requests are the critical medium through which the software at Wikia
is created and changed. This forum should be leveraged to improve the quality of
software development and invite more open discourse regarding software
craftsmanship (style, implementation details, patterns, and best practices) both
within and between teams.

Without guidelines, pull requests tend to converge on “minimum viability”. A
“minimally viable” pull request contains a code diff and a title because this is
all that is required to create the pull request on Github.

Pull request guidelines are a foundational step towards better software
development practices. Pull request guidelines will nurture a culture of
broader peer review and lower friction across groups and services.

## Guidelines

This section outlines the guidelines that should be imposed upon pull requests at Wikia.
From this point forward the abbreviation PR will be used in place of “pull request.”

Each PR will:
  1. Include a reference to the Jira ticket it is addressing. This should take the
     following form {team}-{number} tag as the first part of the title where {team} is the
     Jira team and {number} is the jira ticket number. Example: PLATFORM-11. The ticket 
     number should be part of the title not the PR body, because deploy tools
     automatically scan git comments and mark ticket resolution version. Currently
     only commit comments which include the PR title are scanned, whereas PR description not.
  2. Include a title that provides a one sentence overview of the purpose of the
     PR. Abbreviations can be used when necessary. Example: “PLAT-433 put the
     thumbnail under the original dir in the thumb path”.
  3. Contain a description written using complete sentences of the changes being
     made and any context necessary to understand them. Example:
     https://github.com/Wikia/vignette/pull/15.
       1. [optional] An @ message to the stakeholders and reviewers of the PR.
          Example: “/cc @nmonterroso”.
       2. If the PR contains production SQL changes the @SQLReview tag should be
          used to notify the SQL review team.
  4. The source code diff. This is provided by Github automatically.
  5. Tests for behavioral changes made to the code. 
  6. Be reviewed and approved by someone other than the author.

Finally, PRs should be atomic. That is, they should address one item (task, bug,
story, etc). Exceptions to this requirement may exist (such as stories) but they
should be the exception.


## On Merging

Below are some general guidelines on how merging should be handled regardless
of the team. Team specific guidelines should go above and beyond those listed
here.

First, a PR should not be merged to dev/master any of the following apply:
  * A broken build which typically means that unit and or integration tests are
    not passing.
  * Comments asking for clarification that have not been addressed.
  * An explicit request to not merge.
  * A PR that was explicitly identified as a “work in progress”. 

Next, a PR should not be merged unless it has been reviewed by at least one
other person on the team.

## Conclusion

Pull requests are the medium of collaboration and critique within a
Github-based software engineering organization. It is the primary place where
designs are reviewed, standards are maintained, and where critical discourse
can happen around software craftsmanship and professionalism.

Pull requests play a critical role establishing and maintaining software
craftsmanship within an organization. By establishing guidelines that will
improve the visibility and context of software changes we can encourage better
software craftsmanship through peer review. 

## See Also

  * [Tim Pope’s git commit message suggestions](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
  * [MediaWiki commit message guidelines](https://www.mediawiki.org/wiki/Gerrit/Commit_message_guidelines)
  * [MediaWiki code review guidelines](https://www.mediawiki.org/wiki/Gerrit/Code_review)
