# Recovery

{% hint style="info" %}
**Feature under development.** Will be delivered in next releases.
{% endhint %}

In non-transactional environments, recovery mechanism will allow you execute some actions when a changeSet fails. It works retrospectively in reverse order, so when a changeSet fails, it will run all the recovery methods associated with the executed changeSets, starting from the most recently executed.

#### Scenario

| Execution order | ChangeSet | Recovery | State |
| :--- | :--- | :--- | :--- |
| 1st | changeSet1 | recovery\_changeSet1 | EXECUTED |
| 2nd | changeSet2 | no\_recovery | EXECUTED |
| 3rd | changeSet3 | recovery\_changeSet3 | EXECUTED |
| 4th | changeSet4 | recovery\_changeSet4 | FAILED |

Assuming a non-transactional environment, after changeSet4 fails, as it's not marked with `failFast` to `false`,  Mongock will run the recovery process, which will run in order: recovery\_changeSet4, recovery\_changeSet3, recovery\_changeSet1. 

Please notice that changeSet2 does not provide any recovery, so it's ignored.

