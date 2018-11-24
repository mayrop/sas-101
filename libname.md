## Libraries

For SAS, a data library is a set of files (`.sas7bdat`) that are stored in the same physical location of a comptuter. We need to tell SAS the physical location of the datasets you're going to read from.

```
libname orion "/courses/myinput";
libname output "/home/v0008/sasuser.v94/";
```

**Where:**
- **`libname`**: It's the instruction for letting SAS know that you're setting a library.
- **`orion`**: This is just a variable name that will tell sas to point `orion` to a subdirectory inside `/courses/myinput`. `orion` does not actually mean anything, we can use any variable name as long as it complies with the rules below.
- **`output`**: This is just a variable name that points to a folder that we can `write` from.
- **`/courses/myinput`**: That's the physical location of the library. Click here if you don't know how to get this value? 

From a technical perspective, you don't need two different folders, but from a practical standpoint it's good to separate the folders where your read from and write to so you don't end up overwriting datasets.

## Rules for 'nicknames' (variable names) libraries
* It needs to have between 1 and 8 chars.
* It needs to begin with a letter or underscore.
* It should only contain letters, numbers, or underscores.

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

# Importing from other formats

```sas
filename reffile 'C:\My Documents\my-sample-dataset.csv';
```

```sas
proc import databafile=reffile
    dbms=csv
    out=output.mydataset;
    getnames=yes;
run;
```