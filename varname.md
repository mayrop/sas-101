## Variable names

### Rules for variable names:
* It cannot start with a number
* It cannot contain full stops or else SAS will go look for it in a certain library
* It needs to begin with an English letter or underscore.
* It should **only** contain letters, numbers, or underscores. (no spaces or other symbols)
* The maximum length is 32 characters.

### Example of __valid__ variable names:
* ``VAR1``
* ``Var1``
* ``December_2007``
*`` _count_ ``

### Example of __invalid__ variable names:
* ``DateDD/MM/YY``
* ``ThisVariableHasMoreThan32Characters``
* ``1st_SBP_measure``
* ``December.2007``

## Renaming variables
```sas
data orion.file;
set orion.file(rename = (newname=oldname));
run;
```

or

```sas
data orion.file;
set orion.file;
newname=oldname;
drop oldname;
run;
```

