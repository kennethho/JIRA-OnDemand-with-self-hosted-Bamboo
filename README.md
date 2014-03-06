JIRA-OnDemand-with-self-hosted-Bamboo
=====================================
To generate the diagram with graphviz in a shell:
```Shell
$ dot -T png jira-workflow.dot -o jira-workflow.png
```
## Diagram
![alt text](https://raw.github.com/kennethho/JIRA-OnDemand-with-self-hosted-Bamboo/master/jira-workflow.png "Workflow")

## Roles
1. User: Anyone with an account to create ticket on Zillians JIRA
2. Developer: Any Thor developer
3. Owner: A Thor developer responsible for handling the ticket
4. Bamboo (buildbot): Self-hosted Continue Integration system used for Thor
5. Reviewer: A Thor developer who is assigned as a reviewer when Owner sends PR on Bitbucket

## States and Transitions
1. A newly created ticket is in **Open** state. The ticket is to be assigned to an owner, either at the time of creation or at a later time. Note that a ticket must have an **owner** so it could be worked on
2. The ticket transit to **Accepted** state when it is accepted by the assigned **owner**. **Owner** should start investigation and work on the issue
  1. **Owner** sends a PR (and assigns one or more **reviewers** on Bitbucket), which triggers a build and test flow on our Bamboo buildbot
    1. If buildbot finds the PR to **pass**. It sends notification emails to ticket **Owner** and **Reviewers**, triggers the ticket to transit to **Reviewing** state and updates the ticket
    2. If buildbot finds the PR to **fail**. It sends notification emails to ticket **Owner** and **reviewers** and updates the ticket
  2. **Owner** re-**assign**s the ticket for:
    1. The ticket was incorrectly assigned. Current owner is not the right person for handling the ticket
    2. The ticket reqiures more information (log, evironment setting and etc) to be processed
    3. The ticket is invalid or a duplicate
3. When there are sufficient reviews on a successfully built and tested PR. The owner may:
  1. Merge the PR and **close** the ticket
  2. **Recall** the PR and rework on the ticket. The ticket transits back to **Accepted** state

Note that **Reviewer approves** and **Reviewer disapproves** are pseudo transitions, nothing really happens on JIRA side. When reviewing using Bitbucket, a reviewer could either **approve** or make disapproving comments. However, AFAIK, there is no way to reflect reviewer actions on Bitbucket to JIRA.
