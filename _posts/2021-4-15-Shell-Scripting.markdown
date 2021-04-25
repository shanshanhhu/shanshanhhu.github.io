---
title: Shell Scripting
date: 2021-04-15 17:18:22 +0800
categories: [linux, linux]
tags: [linux]
---
# What is Shell?
![](/assets/img/sample/Components-of-Shell-Program.png)
A Kernel is at the nucleus of a computer. It makes the communication between the hardware and software possible. While the Kernel is the innermost part of an operating system, a shell is the outermost one.

# Write and Run a Shell Script
![Steps to Create Shell Script](/assets/img/sample/create-shell-script.png)
## add execute permission to a script
> chmod +x shellScript.sh

## execute a script
- ./shellScript.sh
- sh shellScript.sh
- bash shellScript.sh

### run a script using debugging options
- debugging options
  - `-v` (short for verbose) – tells the shell to show all lines in a script while they are read, it activates verbose mode.
  - `-n` (short for noexec or no ecxecution) – instructs the shell read all the commands, however doesn’t execute them. This options activates syntax checking mode.
  - `-x` (short for xtrace or execution trace) – tells the shell to display all commands and their arguments on the terminal while they are executed. This option enables shell tracing mode.
- usage
> bash -x shellScript.sh



## Shell Variables
![Shell Variables](/assets/img/sample/shell-variables.jpg)

```shell script
NAME="Zara Ali"
echo $NAME

# Read-only Variables
NAME="Zara Ali"
readonly NAME
NAME="Qadiri"  # Result : /bin/sh: NAME: This variable is read only.

# Unsetting Variables
NAME="Zara Ali"
unset NAME   #  You cannot use the unset command to unset variables that are marked readonly.
echo $NAME   # print nothing
```

### Special Variables
![Linux-Special-Variables](/assets/img/sample/Linux-Special-Variables.png)
The `$?` variable represents the exit status of the previous command. Exit status is a numerical value returned by every command upon its completion.
As a rule, most commands return an exit status of 0 if they were successful, and 1 if they were unsuccessful.

## Shell Arrays

```shell script
# for bash shell array initialization
# first format
array_name=(value1 ... valuen)
# second format
declare -a array_name
# third format
declare -a my_array=( "Hydrogen" "Helium" "Lithium" "Beryllium" )
# access array values
${array_name[index]}
# access all the items in an array
${array_name[*]}
${array_name[@]}

# demo
NAME[0]="Zara"
NAME[1]="Qadir"
NAME[2]="Mahnaz"
NAME[3]="Ayan"
NAME[4]="Daisy"
echo "First Index: ${NAME[0]}"
echo "Second Index: ${NAME[1]}"
```

## Shell Basic Operators(Bourne Shell)
[basic operators](https://www.tutorialspoint.com/unix/unix-basic-operators.htm)
all the conditional expressions should be inside square braces with spaces around them, for example \[ $a == $b ] is correct whereas, \[$a==$b] is incorrect.
### Arithmetic Operators
Assume variable a holds 10 and variable b holds 20.
![](/assets/img/sample/arithmetic-operaters.png)
### Relational Operators
Assume variable a holds 10 and variable b holds 20.
![](/assets/img/sample/relational-operators.png)
### Boolean Operators
Assume variable a holds 10 and variable b holds 20.
![](/assets/img/sample/boolean-operators.png)
### String Operators
Assume variable a holds "abc" and variable b holds "efg".
![](/assets/img/sample/string-operators.png)
### File Test Operators
Assume a variable file holds an existing file name "test" the size of which is 100 bytes and has read, write and execute permission on.
![](/assets/img/sample/file-test-operators.png)

## if condition
[if condition in shell script](https://www.educba.com/if-condition-in-shell-script/?source=leftnav)
1. simple if

```
if <expression for checking>;
then
<set of commands to be executed>
fi
```
2. if else

```
if <expression for checking>;
then
<set of commands to be executed>
else
<set of other commands to be executed>
fi
```
3. if elif else fi

```
if <expression 1 for checking>;
then
<set of commands to be executed>
.
elif <expression 2 for checking>
then
<set of other commands to be executed>
.
else
<set of else set of commands to be executed>
fi
```
4. Boolean if condition

```
if [<test_Condition_1>] && [<test_Condition_2>] then   // '&&', '||'
<set_of_commands_to_be_executed>
fi
```
- Example:
```
rem=$(( $1 % 2 ))  // (( )) do arithmetic calculation
if [ $rem -eq 0 ]
then
echo "Even Number"
else
echo "Odd Number"
fi
```

## Switch Case
- Syntax:
```
case $var in pattern) commands to execute;;
pattern1) commands to execute;;
pattern2) commands to execute;;
pattern3) commands to execute;;
*)
Default condition and commands to execute;;
esac

# multiple patterns
case $var in pattern|pattern1|pattern2) list of commands need to execute;;
pattern3| pattern4| pattern5) list of commands need to execute;;
pattern6) commands need to execute;;
*)
Default condition and statements need to execute
esac
```
- Example:
```shell script
fruit = "kiwi"
case $fruit in "apple") echo "apple is tasty";;
"banana") echo "I like banana";;
"kiwi") echo "Newzeland is famous for kiwi";;
*)
Echo "default case";;
esac

# format date
NOW=$(date +"%a")  # +FORMAT， use date --help for details.
case $NOW in Mon) echo "Full backup";;
Tue|Wed|Thu|Fri) echo "Partial backup";;
Sat|Sun) echo "No backup";;
*) ;;
esac
```

## Loops
### while Loop
- Syntax:
```
while [ condition ] do
command1
command2
done
```
- Example:
```shell script
# display numbers from 0 to 2
i=0

while [ $i -le 2 ]
do
  echo Number: $i
  ((i++))       # ((i++)) or let "i++" or i=$((i++))
done

# Infinite while Loop
while :    # you can also use true
do
  echo "Press <CTRL+C> to exit."
  sleep 1
done

# a single-line equivalent
while :; do echo 'Press <CTRL+C> to exit.'; sleep 1; done
```
### for Loop
- Syntax:
```
for var in list
do
command 1
command 2
done
```
- Example:
```shell script
# Loop over strings
for element in Hydrogen Helium Lithium Beryllium
do
  echo "Element: $element"
done

# Loop over a number range
# {START..END}
for i in {0..3}  # iterate through all numbers from 0 to 3
do
  echo "Number: $i"
done

# {START..END..INCREMENT}
for i in {0..20..5}
do
  echo "Number: $i"
done

# Output
Number: 0
Number: 5
Number: 10
Number: 15
Number: 20

# Loop over array elements
BOOKS=('In Search of Lost Time' 'Don Quixote' 'Ulysses' 'The Great Gatsby')

for book in "${BOOKS[@]}"; do
  echo "Book: $book"
done

```

### until Loop
- Syntax:
```
until [CONDITION]
do
  [COMMANDS]
done
```
- Example:
```shell script
counter=0

until [ $counter -gt 5 ]
do
  echo Counter: $counter
  ((counter++))
done

```

### `break` and `continue` Statements
```shell script
i=0

while [ $i -lt 5 ]
do
  echo "Number: $i"
  ((i++))
  if [[ "$i" == '2' ]]; then  # equivalent: if [ $i -eq 1 ]; then
    break
  fi
done

echo 'All Done!'

````

### Loops Examples
1. Read a File Line By Line
```shell script
file=/etc/passwd

while read -r line; do  # When reading file line by line, always use read with the -r option to prevent backslash from acting as an escape character.
  echo $line
done < "$file"  # using input redirection (< "$file") to pass a file to the read command

```

2. Renaming files with spaces in the filename
```shell script
for file in *\ *; do
  mv "$file" "${file// /_}"  # replace the space with and underscore(_)
done

```

3. Changing file extension
```shell script
for file in *.jpeg; do
    mv -- "$file" "${file%.jpeg}.jpg"
done

```

## Functions
- Syntax:
1. first format
```
function_name () {
  commands
}

# single line version
function_name () { commands; }
```
2. second format
```
function function_name {
  commands
}

# single line version
function function_name { commands; }
```

### Local Variables
In Bash, all variables by default are defined as global, even if declared inside the function.Local variables can be declared within the function body with the local keyword and can be used only inside that function.
```shell script
#!/bin/bash

var1='A'
var2='B'

my_function () {
  local var1='C'
  var2='D'
  echo "Inside function: var1: $var1, var2: $var2"
}

echo "Before executing function: var1: $var1, var2: $var2"

my_function

echo "After executing function: var1: $var1, var2: $var2"

# Output
Before executing function: var1: A, var2: B
Inside function: var1: C, var2: D
After executing function: var1: A, var2: D
```
### Return Values
Bash functions don’t allow you to return a value when called. When a bash function completes, its return value is the status of the last statement executed in the function, 0 for success and non-zero decimal number in the 1 - 255 range for failure.

The return status can be specified by using the return keyword, and it is assigned to the variable $?. The return statement terminates the function.
```shell script
#!/bin/bash

my_function () {
  echo "some result"
  return 55  #
}

my_function
echo $?

# Output
some result
55

# assign the result of the function to a global variable
#!/bin/bash

my_function () {
  func_result="some result"
}

my_function
echo $func_result
```
### Passing Arguments to Bash Functions

```shell script
#!/bin/bash

greeting () {
  echo "Hello $1"   # $0 is reserved for function name. The passed parameters are $1, $2, $3
}

greeting "Joe"

# Output
Hello Joe
```

# Bash Shell Parameter Expansions
[Introduction to Bash Shell Parameter Expansions](https://linuxconfig.org/introduction-to-bash-shell-parameter-expansions)

# debug a shell script
[5 Simple Steps On How To Debug A Bash Shell Script](https://www.shell-tips.com/bash/debug-script/)
## Using set Shell Built-in Command
> set --help // for details
Using the set built-in command to debug a given section of a shell script.
- To enable debugging mode, use:
> set -option
- To disable debugging mode, use:
> set +option
- if we have enabled several debugging modes in different segments of a shell script, we can disable all of them at once like so:
> set -

# Some useful commands
## set
## sleep
> sleep <amount>\[Suffix]  // suffixs are s(seconds)(default), m(minutes), h(hours), d(days)
> sleep 0.05m
## read
[bash read](https://linuxize.com/post/bash-read/)
```shell script
# syntax
read [options] [name...]

# read values from terminal
read var1 var2

# pass the standard input to read
# using piping(|)
echo "Hello, World!" | (read var1 var2; echo -e "$var1 \n$var2")
# using a here string
read -r var1 var2 <<< "Hello, World!"
printf "var1: %s \nvar2: %s\n" "$var1" "$var2"

# By default, read interprets the backslash as an escape character, which sometimes may cause unexpected behavior. To disable backslash escaping, invoke the command with the -r option.
read <<< "Hello, \tWorld!"
printf %s "$REPLY"
# Output
Hello, tWorld!
# add -r option
read -r <<< "Hello, \tWorld!"
printf %s "$REPLY"
# Output
Hello, \tWorld!

# Changing the Delimiter (IFS)
echo "Linux:is:awesome." | (IFS=":" read -r var1 var2 var3; echo -e "$var1 \n$var2 \n$var3")
# When specifying multiple delimiters, assign the characters to the IFS variable without a space between them.
echo 'Linux_is-awesome.' | (IFS="-_" read -r var1 var2 var3; echo -e "$var1 \n$var2 \n$var3")
#Output
Linux
is
awesome.

# Prompt String (-p)
while true; do
    read -r -p "Do you wish to reboot the system? (Y/N): " answer
    case $answer in
        [Yy]* ) reboot; break;;
        [Nn]* ) exit;;
        * ) echo "Please answer Y or N.";;
    esac
done

# If the shell script asks users to enter sensitive information, like password, use the -s option that tells read not to print the input on the terminal:
read -r -s -p "Enter your password: "

# Assign the words to Array
read -r -a MY_ARR <<< "Linux is awesome."

for i in "${MY_ARR[@]}"; do
  echo "$i"
done
```

## Concatenate Strings
```shell script
var_1="Concatenate"
var_2="Strings"

# format1
$var_1$var_2
# format2
var_1+="Strings"
# format3
printf -v printf_variable "Concatenate Strings"
echo $printf_variable
```
