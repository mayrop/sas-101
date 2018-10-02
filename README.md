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

## Rules for 'nicknames' (variable names) libraries
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

## Examples

<table>
    <tbody>
        <tr>
            <td>
                <pre><code>Valid Library Name</code></pre>
            </td>
            <td>
                <pre><code>libname _input_ "/home/v0008/sasuser.v94";</code></pre>
            </td>           
            <td>
                <pre><code>libname _input_ "/courses/dc36fc35ba27fe300";
<br>NOTE: Libref _INPUT_ was successfully assigned as follows:
<br>Engine: V9
<br>Physical Name: /home/v0008/sasuser.v94</code></pre>
            </td>                       
        </tr>
        <tr>
            <td>
                <pre><code>Invalid Library Name</code></pre>
            </td>
            <td>
                <pre><code>libname myinputlibrary "/home/v0008/sasuser.v94";</code></pre>
            </td>           
            <td>
                <pre><code>libname myinputlibrary "/home/v0008/sasuser.v94";
</br>ERROR: myinputlibrary is not a valid SAS name.<br><br/>
</br>ERROR: Error in the LIBNAME statement.</code></pre>
            </td>                       
        </tr>       
    </tbody>
</table>


## Procedures

Per [documentation](http://support.sas.com/documentation/cdl/en/lrcon/62955/HTML/default/viewer.htm#a000992094.htm) the PROC step consists of a group of SAS statements that call and execute a procedure. It's basically a way to ask SAS to perform some defined action. 

### Procedure contents

This procedure describes the contents of a dataset and prints the directory of the SAS library. Basically it's a way to know the structure of a dataset.

**Code:**

```sas
proc contents data = output.nonsales;
run;
```

**Where:**
- **`proc`**: This is just the way to indicate SAS "execute" or "call".
- **`contents`**: This is a defined SAS statement. For a full list of statements, see the [Base SAS Procedures Guide](https://support.sas.com/documentation/cdl/en/proc/61895/PDF/default/proc.pdf).
- **`data`**: This is a parameter to indicate SAS where to get the dataset from. It specifies the input dataset.
- **`run`**: This is the way to indicate SAS that we're done giving instructions so that all the code before it can be executed. 

Which will generate the following output:  
<img src="https://github.com/mayrop/sas-101/blob/develop/resources/images/proc-contents-01.png" height="200px"/>

There are different options that can be set to modify the output of this procedure, for example:
```sas
proc contents data = output.nonsales out=mydata (keep= name varnum) noprint VARNUM;
run;
```

```sas
data test.mydataset; * this is where it's going to be saved;
    set test.mydataset; * this is where it's read from;
    money = money * 1.1; * this is setting a new column (or orverwriting) incrementing it's value by 0.1;
run;
```

```sas
proc freq data=test.mydataset;
    table country / NOCUM; 
run;
```

/* 
 * NOCUM = https://support.sas.com/documentation/cdl/en/statug/63033/HTML/default/viewer.htm#statug_freq_sect010.htm
 * 
 */


```
proc freq data=test.mydataset;
    where position = "Manager";
    table country * job_title / nopercent norow nocol;
run;
```

/* 
 * nopercent = https://support.sas.com/documentation/cdl/en/statug/63033/HTML/default/viewer.htm#statug_freq_sect010.htm
 * 
 */

```sas
data output.mynewdataset;
    set test.mydataset; *;
    age = (existing_date - hbd_date) / 365.25;
run;
```

```sas
proc means data=output.ages maxdec=2 mean clm;
    class gender;
    var age;
run;
```

## Importing Database from CSV to SAS
```sas
filename reffile '/home/v0008/sasuser.v94/my-sample-dataset.csv';
```

```sas
proc import databafile=reffile
    dbms=csv
    out=output.mydataset;
    getnames=yes;
run;
```
