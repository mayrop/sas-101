# Dates

For SAS, a date is number of days from 01/01/1960 (day 0).
When we have a date we need to do the following:

- Convert the date into numbers
```
data example_dsn2;
	set example_dsn1;
	dob = mdy(dobm, dobd, doby);
run;
```

- Display the numbers as dates
The following are some formats:

| SAS value |  Format     |  We see          | 
|-----------|-------------|------------------| 
| 21109     |  date9.     |  17Oct2017       | 
| 21109     |  worddatx.  |  17 October 2017 | 
| 57600     |  timeampm9. |  4:00 PM         | 
| 57600     |  time.      |  16:00:00        | 

Applying a format to a date means displaying a number as a Date.

## Calculations with dates

Since dates are the number of days since 1/1/1960, we can do simple subtractions.
To find out the number of days between two dates: ``date2 – date1;``

If we want to use a date as reference in a datastep, we need to express it like this 
``“ddmonyyyy”d``.
For example: '12Oct2018'd


```sas
data example_dsn2;
	set example_dsn1;
	if id = 24 then dobd = 30;
	dob = mdy(dobm, dobd, doby);
	age = ('12Oct2018'd - dob)/365.25;
	format dob date9.;
	keep id dobd dobm doby dob age;
run;
```
