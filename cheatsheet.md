### Important Tips
* SAS is case insensitive, so it's the same to do `proc contents` and
`PROC CONTENTS`. However, it's always advised to keep consistency
* When referrencing a library, you will use `{folder}`.`{dataset}`, 
where
	* `{folder}` is the name you specified when you imported the library
via `libname`
	* `dataset` is the name of your dataset.

## Comments in SAS
Comments are useful to keep your code readable
```
*message;
```

or 
```
/* My comment */
```

## Importing Libraries
 
```
libname mylib "C:\My Code\My Folder";
```

## Importing Libraries to SAS format (from other formats - csv, excel, etc.)
Instructions:
* Use File -> Import Data. Ensure you save the SAS code, and can read it in and run it

```sas
proc import out=outlib.mydataset 
        datafile="C:\My Code\My Folder\example.csv" 
		dbms=csv replace;
    getnames=yes;
    datarow=2; 
run;
```

------------------------

# Data Statements
```sas
data allsales;
	set australia unitedstates;
run;
```

```sas
data dataset_example;
	set dataset_example(rename = (county = country));
run;
```

```sas
data dataset_example;
	set dataset_example;
	if student_points < 18.5 and student_points ne . then classroom = 1;
	else if student_points >= 18.5 and student_points < 25 then classroom = 2;
	else if student_points >= 25 and student_points < 30 then classroom = 3;
	else if student_points >= 20 then classroom = 4;
run;
```

```sas
data dataset_example;
	set dataset_example;
	drop student_points;
run;
```

```sas
data dataset_example;
	set dataset_example;
	date_of_birth = mdy(date_of_birth_month, date_of_birth_day, date_of_birth_year);
run;
```

```sas
data dataset_example;
	set dataset_example;
	format dob dov worddatx.;
run;
```

```sas
data dataset_example;
	set dataset_example;
	if is_married ne "yes" then is_married = 'no';
run;
```

```sas
data dataset_example;
	set source.dataset_example;
	name = catx(' ', first_name, last_name);
	
	if points > 100 then do;
		winner = 'Yes';
		money = money * 0.9;
	end;
	else winner = 'No';
	
	where upcase(scan(city, 1, ' ')) = 'BOSTON';
	
	format money dollar10.;
run;
```

```sas
data work.dataset_example;
	set source.dataset_example;
 	car_name = compress(car_name, "()", "k");
 	car_model = compress(car_model, " ");
run;
```

------------------------ 

# proc contents
```sas
proc contents data = mylib.dataset_example order = varnum;
run;
```
### Required syntax
* `proc contents` is the procedure name
* `data` is the way to specify the source where you want the information from
* `mylib.dataset_example` is the dataset source
* `run;` Ends the procedure

### Optional
* `order = varnum` option will display the variables in order, rather than alphabetically


------------------------

# proc freq
```sas
proc freq data = mylib.dataset_example;
	table gender / nocum nopercent;
run;
```

```sas
proc freq data = mylib.dataset_example;
	table gender * country;
run;
```

```sas
ods noproctitle;
proc freq data = mylib.dataset_example;
 	title "Example table";
	table gender;
	where country = "MX";
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
	* `nocum` Removes the Cumulative Frequency / Percentage
	* `nopercent` Removes the Percentage
* Filtering can be done through `where` clause

------------------------

# proc means
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

------------------------

# proc format
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

# merge

```
data dataset_example;
	merge dataset1 dataset2;
	by id;
run;
```

* *Important* You need to sort first!

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

```sas
data dataset_example;
	merge dataset1 dataset2;
	by id;
run;
```

The code below will only include rows if they exist in b.
```sas
data dataset_example;
	merge dataset1(in = a) dataset2(in = b);
	by id;
	if b;
run;
```

------------------------

# proc print

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

# proc copy
```sas
proc copy in=work out=mylib;
	select nonsales;
run;
```

* in (where to copy from)
* out (where to copy to)

------------------------

# PDFs

```sas
ods pdf file = "filepath / fileName.pdf" style = OCEAN

proc means data =  mean clm;
	class building;
	var has_soul;
	where year = 2016;
run;
```
