# Events

#### **Since version 4.1.19**

The goal of Mongock events is to notify when the migration process has finished successfully or with errors, which would probably means the transaction was interrupted or not even started.  
  
Depending on the type of Mongock runner you are using\(standalone or Spring\) you will configure it differently.

## How to use it

### With spring runner

If you are using spring you need...

### With standalone

In this case you don't have a framework around you to provide the events ecosystem, so you need to provide two handles for both events, success and fail migration events...



 

\*\*\*\*

