JIRA-OnDemand-with-self-hosted-Bamboo
=====================================
To generate the diagram with graphviz in a shell:
```Shell
$ dot -T png jira-workflow.dot -o jira-workflow.png
```
![alt text](https://raw.github.com/kennethho/JIRA-OnDemand-with-self-hosted-Bamboo/master/jira-workflow.png "Workflow")

1. When a ticket is created, it is in **Open** state. The ticket is to be assigned to an owner, either at the time of creation or at a later time. At this state Note that a ticket must have an owner so it could be worked on
2. The ticket transit to **Accepted** state when it is accepted by the assigned owner. The owner should start investigating and work on the issue
  1. The owner finds its a valid issue and decides to work on a solution to the ticket. When a solution is ready, the owner sends a PR (and assigns one or more reviewers), which triggers a build and test flow on our buildbot
    1. If buildbot finds the PR a **pass**. It sends notification emails to ticket owner and reviewers, triggers the ticket to transit to **Reviewing** state and updates the ticket
    2. If buildbot finds the PR a **fail**. It sends notification emails to ticket owner and reviewers and updates the ticket
  2. The owner looks into the ticket and finds the ticket
    1. Incorrectly assigned. He/she is not the right person for handling the ticket. He/she reassigns the ticket to the right person
    2. Reqiures more information (log, evironment setting and etc) to progress. He/she reassigns the ticket to the person (possibly the one who created the ticket) with the information needed
    3. Invalid and rejects (usually by assigning it to someone who is able to provide a 2nd opinion) the ticket
3. When there are sufficient reviews on a successfully built and tested PR. The owner may:
  1. Merge the PR and close the ticket
  2. Recall the PR and rework on the ticket. The ticket transits back to **Accepted** state

