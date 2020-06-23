# Lock

In order to execute the changeLogs, Mongock needs to manage the lock to ensure that one Mongock instance not only executes a changeLog at a time, but also access to the database, as otherwise it could run into a race condition. By default Mongock will ensure the Lock is taken before starting the migration. If it's already taken by some other instance, it will try to acquire it up to 3 times. If it doesn't take it after these 3 times, waiting 4 minutes every time, it will throw an exception and abort the migration. However, you can configure to modify this behaviour, from the maximum number of tries to not throwing the exception if the lock cannot be obtained.

## Overview

Although this is completely configurable, the default, expected and recommended behaviour is that before starting a migration Mongock will acquire the lock, trying up to certain number of times\(if it's already taken or there is any connection issue to the database\). Once taken, it will "reserve" it initially for some number of minutes\(**lockAcquiredForMinutes**\). When that time is near to expire, Mongock it will extend it using the same amount of minutes. This process will be repeatedly performed until the migration is finished.

