## Libraries

For SAS, a data library is a set of files (`.sas7bdat`) that are stored in the same physical location of a comptuter. We need to tell SAS the physical location of the datasets you're going to read from.

```
libname orion "/courses/myinput";
libname output "/home/v0008/sasuser.v94/";
```

**Where:**
- **`orion`**: This is just a variable name that will tell sas to point `orion` to a subdirectory inside `/courses/myinput`. `orion` does not actually mean anything, we can use any variable name as long as it complies with the rules below.
- **`output`**: This is just a variable name that points to a folder that we can `write` from.

From a technical perspective, you don't need two different folders, but from a practical standpoint it's good to separate the folders where your read from and write to so you don't end up overwriting datasets.

### Variable names rules for libraries
* Needs to have between 1 and 8 chars.
* The variable name needs to begin with a letter or underscore.
* It should only contain only letters, numbers, or underscores.

### Example of valid library names:
* `orion`
* `input1`
* `input2`
* `_input_`

### Example of invalid library names:
* `myinputlibrary` (longer than 8 chars)
* `2input` (starts with a number)

If you try to set an invalid name to a library, you will get the following error:
```sas
 72         libname _input_222 "/home/v0008/sasuser.v94";
 ERROR: _input_222 is not a valid SAS name.
 ERROR: Error in the LIBNAME statement.
```

If everything goes smoothly, you'll see the following log when setting up a library:  
| Code                                               | Output                                                                                                                                                                                   |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ``` libname _input_ "/home/v0008/sasuser.v94"; ``` | ```,NOTE: Libref _INPUT_ refers to the same physical library as INPUT_.,NOTE: Libref _INPUT_ was successfully assigned as follows:,Engine:,V9,Physical Name: /home/v0008/sasuser.v94 ``` |

### Documentation
Visit the [official documentation](http://support.sas.com/documentation/cdl/en/lrdict/64316/HTML/default/viewer.htm#a000214133.htm) for further documentation and options.

------------------------

## Procedures

```
proc contents data=output.mydataset;
run;
```

| Data Set Name       | OUTPUT.MYDATASET                                      | Observations         | 1000 |
|---------------------|-------------------------------------------------------|----------------------|------|
| Member Type         | DATA                                                  | Variables            | 14   |
| Engine              | V9                                                    | Indexes              | 0    |
| Created             | 09/29/2018 0:54:26                                    | Observation Length   | 144  |
| Last Modified       | 09/29/2018 0:54:26                                    | Deleted Observations | 0    |
| Protection          |                                                       | Compressed           | NO   |
| Data Set Type       |                                                       | Sorted               | NO   |
| Label               |                                                       |                      |      |
| Data Representation | SOLARIS_X86_64, LINUX_X86_64, ALPHA_TRU64, LINUX_IA64 |                      |      |
| Encoding            | utf-8 Unicode (UTF-8)                                 |                      |      |

### Engine/Host Dependent Information

|-----------------------------------|--------------------------------------------|
| Data Set Page Size                | 131072                                     |
| Number of Data Set Pages          | 2                                          |
| First Data Page                   | 1                                          |
| Max Obs per Page                  | 909                                        |
| Obs in First Data Page            | 883                                        |
| Number of Data Set Repairs        | 0                                          |
| Filename                          | /home/v0008/sasuser.v94/mydataset.sas7bdat |
| Release Created                   | 9.0401M5                                   |
| Host Created                      | Linux                                      |
| Inode Number                      | 26084658                                   |
| Access Permission                 | rw-r--r--                                  |
| Owner Name                        | v0008                                      |
| File Size                         | 384KB                                      |
| File Size (bytes)                 | 393216                                     |


### Alphabetic List of Variables and Attributes

|---------------------------------------------|----------------|------|-----|-----------|-----------|
| #                                           | Variable       | Type | Len | Format    | Informat  |
| 2                                           | Country        | Char | 10  | $10.      | $10.      |
| 3                                           | Item Type      | Char | 15  | $15.      | $15.      |
| 6                                           | Order Date     | Num  | 8   | MMDDYY10. | MMDDYY10. |
| 7                                           | Order ID       | Num  | 8   | BEST12.   | BEST32.   |
| 5                                           | Order Priority | Char | 1   | $1.       | $1.       |
| 1                                           | Region         | Char | 33  | $33.      | $33.      |
| 4                                           | Sales Channel  | Char | 7   | $7.       | $7.       |
| 8                                           | Ship Date      | Num  | 8   | MMDDYY10. | MMDDYY10. |
| 13                                          | Total Cost     | Num  | 8   | BEST12.   | BEST32.   |
| 14                                          | Total Profit   | Num  | 8   | BEST12.   | BEST32.   |
| 12                                          | Total Revenue  | Num  | 8   | BEST12.   | BEST32.   |
| 11                                          | Unit Cost      | Num  | 8   | BEST12.   | BEST32.   |
| 10                                          | Unit Price     | Num  | 8   | BEST12.   | BEST32.   |
| 9                                           | Units Sold     | Num  | 8   | BEST12.   | BEST32.   |

proc contents DATA=test.mydataset out=mydata (keep= name varnum) noprint VARNUM;
run;

data test.mydataset; * this is where it's going to be saved;
    set test.mydataset; * this is where it's read from;
    money = money * 1.1; * this is setting a new column (or orverwriting) incrementing it's value by 0.1
run;

proc freq data=test.mydataset;
    table country / NOCUM; 
run;

/* 
 * NOCUM = https://support.sas.com/documentation/cdl/en/statug/63033/HTML/default/viewer.htm#statug_freq_sect010.htm
 * 
 */

proc freq data=test.mydataset;
    where position = "Manager";
    table country * job_title / nopercent norow nocol;
run;

/* 
 * nopercent = https://support.sas.com/documentation/cdl/en/statug/63033/HTML/default/viewer.htm#statug_freq_sect010.htm
 * 
 */

data output.mynewdataset;
    set test.mydataset; *;
    age = (existing_date - hbd_date) / 365.25;
run;

proc means data=output.ages maxdec=2 mean clm;
    class gender;
    var age;
run;

## Importing Database from CSV to SAS
```
filename reffile '/home/v0008/sasuser.v94/my-sample-dataset.csv';
```

```sas
proc import databafile=reffile
    dbms=csv
    out=output.mydataset;
    getnames=yes;
run;
```
