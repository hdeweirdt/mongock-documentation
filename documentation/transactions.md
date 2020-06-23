# Transactions

## Context

Due to Mongo limitations\(until MongoDB 4.0\), there is no way to provide atomicity at ChangeSet level. So a ChangeSet could require more than one execution to be finished, as any interruption could happen, leaving the changeLog in an inconsistent state. If that happens, the next time mongock is executed it will try to finish the changeLog execution, but it could already be half executed.

For this reason, the developer in charge of the Mongock migration's design, should make sure that:

* **ChangeLog is idempotent**: As changeLog can be interrupted at any time, it will need to be executed again.
* **ChangeLog is Backward compatible \(If high availability is required\)**: While the migration process is taking place, the old version of the software is still running. During this time could happen\(and probably will\) that the old version of the software is dealing with the new version of the data. Could even happen that the data is a mix between old and new version. This means the software must still work regardless of the status of the database. In case the developer is aware of this and still decides to provide a non-backward-compatible changeSet, he should know it's a detriment to high availability.
* **ChangeLog reduces its execution time in every iteration**: This is harder to explain. As said, a changeLog can be interrupted at any time. This means a specific changeLog needs to be re-run. In the undesired scenario where the changeLog's execution time is grater than the interruption time\(could be Kubernetes initial delay\), that changeLog won't be ever finished. So the changeLog needs to be developed in such a way that every iteration reduces its execution time, so eventually, after some iterations, the changeLog finished.
* **ChangeLog's execution time is shorter than interruption time**: In case the previous condition cannot be ensured, could be enough if the changeLog's execution time is shorter than the interruption time. This is not ideal as the execution time depends on the machine, but in most case could be enough.

