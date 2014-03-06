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
2. Developer: A Thor developer
3. Owner: A **Developer** responsible for handling the ticket
4. Bamboo (buildbot): Self-hosted Continue Integration system used for Thor
5. Reviewer: A **Developer** who is assigned as a reviewer when Owner sends PR on Bitbucket

## States and Transitions
1. A newly created ticket is in **Open** state. The ticket is to be assigned to an **Owner**, either at the time of creation or at a later time. Note that a ticket must have an **Owner** so it could be worked on. Feel free to assign issues to yourself :-)
2. The ticket transits to **Accepted** state when it is accepted by the assigned **Owner**. This **Accepted** state is a clear signal that the assigned **Owner** is aware of the ticket and is (potentially) working towards a solution
  1. **Owner** sends a PR (and assigns one or more **reviewers** on Bitbucket), which triggers a build and test flow on our Bamboo buildbot
    1. If buildbot finds the PR to **pass**. It sends notification emails to ticket **Owner** and **Reviewer**s, triggers the ticket to transit to **Reviewing** state and updates the ticket
    2. If buildbot finds the PR to **fail**. It sends notification emails to ticket **Owner** and **Reviewer**s and updates the ticket
  2. **Owner** re-**assign**s the ticket for:
    1. The ticket was incorrectly assigned. Current **Owner** is not the right person for handling the ticket
    2. The ticket reqiures more information (log, version, evironment setting and etc) to be processed
    3. The ticket is invalid or a duplicate
3. When there are sufficient reviews on a successfully built and tested PR. The owner may:
  1. Merge the PR and **close** the ticket
  2. **Recall** the PR and rework on the ticket. The ticket transits back to **Accepted** state

Note that **Reviewer approves** and **Reviewer disapproves** are pseudo transitions, nothing really happens on JIRA side. Two things in particular make this transition very challenging to automate:

1. When reviewing using Bitbucket, all that a reviewer could do are making comments and optionally **approve**s a PR. AFAIK, there is no way to reflect reviewer actions on Bitbucket to JIRA
2. Even if we could somehow make Bitbucket send trigger for reviewer action to JIRA (I am sure we could, given sufficient time to experiment). Do we want the system to wait for approvals from all reviewers? If not, how many should the system wait before making the transition? Majority of assigned reviewers? At least one reviewer? What if it's a emergency, and the issue is obvious and trivial, and it's 2 am in the morning? This is a difficult call, we leave the decision to people who we trust and do the right things
