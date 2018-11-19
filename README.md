## Table of contents

* [Important Tips](#important-tips)
* [Importing Libraries](#importing-libraries)
    * [Importing your datasets](#importing-your-datasets)
    * [Other Extensions (CSV, XLS...)](#converting-other-extensions---csv-excel-etc)
* [Data Statements](#data-statements)
    * [Merging](#merging)
* [Variables](#variables)
* [Procedures](#procedures)
    * [proc contents](#proc-contents)
    * [proc freq](#proc-freq)
    * [proc means](#proc-means)
    * [proc format](#proc-format)
    * [proc print](#proc-print)
    * [proc copy](#proc-copy)
    * [proc sort](#proc-sort)
* [Macros](#macros)

## Important Tips
* SAS is case insensitive, so it's the same to do `proc contents` and
`PROC CONTENTS`. However, it's always advised to keep consistency
* When referrencing a library, you will use `{folder}`.`{dataset}`, 
where
    * `{folder}` is the name you specified when you imported the library
via `libname`
    * `dataset` is the name of your dataset.


## Importing Libraries

For SAS, a data library is a set of files (`.sas7bdat`) that are stored in the same physical location of a comptuter. We need to tell SAS the physical location of the datasets you're going to read from.

### Importing your datasets
```
libname mylib "C:\My Code\My Folder";
```

### Converting other extensions - csv, excel, etc.
Instructions:  
* Use `File -> Import Data`. Ensure you save the SAS code, and can read it in and run it:

```sas
proc import out=outlib.mydataset 
        datafile="C:\My Code\My Folder\example.csv" 
        dbms=csv replace;
    getnames=yes;
    datarow=2; 
run;
```

For further details about libraries, [click here](libname.md).

------------------------

# Data Statements
```sas
/* 
    The following code will create a new dataset called *dataset_output*
    concatenating the two sources (dataset_source_1 dataset_source_2).
    It will also add a new column monthly_sales.
*/

data dataset_output;
    set dataset_source_1 dataset_source_2;

    monthly_sales = daily_sales * 1.1;
run;
```

```sas
/* 
    The following code will create a new dataset called *dataset_output*
    from the contents of the dataset dataset_source.
    It will also rename the column countri to country. 
*/
data dataset_output;
    set source.dataset_source(rename = (countri = country));
run;
```

```sas
data dataset_output;
    set source.dataset_source;

    /* The code below will concatenate two columns */
    name = catx(' ', first_name, last_name);

    /* The code below will set the column classroom */
    if student_points < 10 and student_points ne . then classroom = 1;
    else if student_points >= 10 then classroom = 2;
    else classroom = 3;

    /* The code below will set the column is_married */
    if is_married ne "yes" then is_married = 'no';

    /* The code below will set the column winner and also change money */
    if points > 100 then do;
        winner = 'Yes';
        money = money * 0.9;
    end;
    else winner = 'No';

    /* Change car_name to only keep [] in the column */
    car_name = compress(car_name, "[]", "k");

    /* Change car_model to remove all spaces */
    car_model = compress(car_model, " ");

    /* Adding a new column of type date */
    date_of_birth = mdy(date_of_birth_month, date_of_birth_day, date_of_birth_year);

    format date_of_birth worddatx.;
    format money dollar10.;

    /* The code below will filter the dataset so only rows where first word in city = boston are retrieved */
    where upcase(scan(city, 1, ' ')) = 'BOSTON';

    /* The code below will convert state to uppercase */
    state = upcase(state);

    drop student_temp_score;
run;
```


## Merging Dataset

For merging datasets, it's **important** that you sort first! Otherwise there will be missing rows from the second dataset. See `proc sort`

```sas
data dataset_example;
    merge dataset1 dataset2;
    by id;
run;
```

```sas
/*
    The code below will only include rows if they exist in b.
*/    
data dataset_example;
    merge dataset1(in = a) dataset2(in = b);
    by id;
    if b;
run;
```
------------------------
# Variables

## Variable Names
### Rules for variable names:
* It cannot start with a number
* It cannot contain full stops or else SAS will go look for it in a certain library
* It needs to begin with an English letter or underscore.
* It should ***only*** contain letters, numbers, or underscores. (no spaces or other symbols)
* The maximum length is 32 characters.

### Example of __valid__ variable names:
* ``VAR1``
* ``Var1``
* ``December_2007``
* `` _count_ ``

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

**Problems:**
If we rename the variables, we cannot use their old names to perform operations on them, even inside the snippet of code..
```sas
data example_dsn1;          	
	set example_dsn1(rename = (var1 = v1 var2 = v2));
	var3 = var2 + var1;
run;

*var2 and var1 no longer exist, they have been renamed as v2 and v1.;
```

## Data Types
Variables in SAS can be of 2 types:
* Numeric
	- NAs are shown as ``.``
	- Right-justified by default
* Character
	- NAs are shown as a blank cell
	- Left-justified by default

To find out the data type of a variable:
- The above-mentioned defaults can be used to deduce whether one variable is a character string or numeric.
- We can double click on a column.
- We can use ``proc contents``.

------------------------ 
# Procedures

Per [documentation](http://support.sas.com/documentation/cdl/en/lrcon/62955/HTML/default/viewer.htm#a000992094.htm) the PROC step consists of a group of SAS statements that call and execute a procedure. It's basically a way to ask SAS to perform some defined action. 

## proc contents

This procedure describes the contents of a dataset and prints the directory of the SAS library. Basically it's a way to know the structure of a dataset.

**Syntax:**

```sas
proc contents data = mylib.dataset_example order = varnum;
run;
```

```sas
proc contents data = mylib.dataset_example out=mydata (keep= name varnum) noprint varnum;
run;
```

### Required syntax
* `proc contents` is the procedure name
* `data` is the way to specify the source where you want the information from
* `mylib.dataset_example` is the dataset source
* `run;` Ends the procedure

### Optional
* `order = varnum` option will display the variables in order, rather than alphabetically
* `varnum` this is another way of setting the [option](https://support.sas.com/documentation/cdl/en/statug/63033/HTML/default/viewer.htm#statug_freq_sect010.htm) the order of the variables.

Which will generate the following output:  
<img src="https://github.com/mayrop/sas-101/blob/develop/resources/images/proc-contents-01.png" height="200px"/>

------------------------

## proc freq
```sas
proc freq data = mylib.dataset_example;
    table gender / nocum nopercent norow nocol;
run;
```

```sas
proc freq data = mylib.dataset_example;
    table gender * location;
run;
```

```sas
ods noproctitle;
proc freq data = mylib.dataset_example;
    title "Example table";
    table gender;
    where location = "MX";
run;
```

### Required syntax
* `proc contents` is the procedure name
* `data` is the way to specify the source where you want the information from
* `mylib.dataset_example` is the dataset source
* `run;` Ends the procedure

### Optional
* Supressing Information
Several options can be specified after setting the tables adding `/`, i.e. (`table gender / nocum nopercent`)
    * `norow`
    * `nocol`
    * `nocum` Removes the Cumulative Frequency / Percentage
    * `nopercent` Removes the Percentage. [Documentation](https://support.sas.com/documentation/cdl/en/statug/63033/HTML/default/viewer.htm#statug_freq_sect010.htm)
* Filtering can be done through `where` clause


*Common errors:*
using class in proc freq
```
proc freq data = orion.customer;
	class gender;
	table country;
run;
```
------------------------

## proc means
```sas
proc means data = mylib.dataset_example;
    var age;
run;
```

```sas
proc means data = mylib.dataset_example min max mean clm maxdec = 1;
    class gender;
    var age;
run;
```

*Common errors:*
proc means with a character variable:
```
proc means data = orion.staff mean clm;
	var job_title;
run;
```
------------------------

## proc format
```sas
proc format;
    value rating 1 = 'Very Good'
        2 = 'Good'
        3 = 'Neutral'
        4 = 'Bad';
run;
```

```sas
proc freq data = mylib.dataset_example;
    table overall_rating;
    format overall_rating rating.;
run;
```

```sas
proc format;
    value $shape_format 
        "circle" = "Circle"
        "CIRCLE" = "Circle"
        "rectangle" = "Rectangle"
        "RECTANGLE" = "Rectangle";
run;
```

```sas
proc freq data = mylib.dataset_example;
    table shape;
    format shape $shape_format.;
run;
```

------------------------

## proc print

```sas
options nocenter;
proc print data = dataset_example label;
    var student_id name graduation_date savings;

    format savings dollar10. graduation_date date9.;
    title "My first title";
    title2 "My second title";
    footnote1 "Leave a footnote 1";
    footnote2 "Leave a footnote 2";

    label student_id = "Student ID" 
        name = "Full Name"
        graduation_date = "Graduation Date" 
        savings = "Savings";

    where graduation_date ge '01Feb2000'd and graduation_date le '30Nov2010'd;
run;
```

------------------------

## proc copy
```sas
proc copy in=work out=mylib;
    select nonsales;
run;
```

* in (where to copy from)
* out (where to copy to)

------------------------

## proc sort
```sas
proc sort data = dataset1;
    by id;
run;
```

```sas
proc sort data = dataset2;
    by id;
run;
```

------------------------

# Out put

## PDFs

### Syntax
```sas
ods pdf <option(s)>;
...procedure name...
ods pdf close;
```

### Example
```sas
ods pdf file = "filepath/filename.pdf"

proc means data =  mean clm;
    class country;
    var sex;
    where year = 2018;
run;

ods pdf close;
```

------------------------

# Macros
```sas
%macro print_smokers(smoker, gender=male);
    proc print data=students;
        where is_smoker=&smoker and gender=&gender;
    run;
%mend;

%print_smokers(smoker=Yes, gender=female);
```

------------------------

# Extra

## Comments in SAS
Comments are useful to keep your code readable
```
*message;
```

or 
```
/* My comment */
```
