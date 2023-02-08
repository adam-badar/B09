
# A1: System Monitoring Tool



## Table of Contents

* [Description](#general-info)
* [Dependencies](#dependencies)
* [Usage](#usage)
* [Functions](#functions)
* [Examples](#examples)


## Description

This code contains a program to gather information about the system, including information about memory usage, system name, machine name, version, release, architecture, user sessions, and CPU usage.
## Dependencies

* GCC compiler

The following libraries and header files are used in the program:

* stdio.h: Standard input/output library in C.
* stdlib.h: Standard Library in C.
* unistd.h: Library with various operating system APIs, such as sleep, read, write, etc.
* string.h: String handling library in C.
* sys/utsname.h: Library for system information such as the * system name, machine name, version, release, etc.
* sys/sysinfo.h: Library for information about system resource utilization.
* sys/resource.h: Library for system resource limits and utilization.
* utmp.h: Library for system user accounting information.
* stdbool.h: Library to support the type 'bool' in C.
* ctype.h: Library for character handling functions.
* math.h: Library for mathematical functions.
## Usage

The program can be compiled using a standard C compiler and executed in the command line. The following code can be used to complie the program: 

`gcc -o mySystemStats mySystemStats.c`

The following executable can be used to run the program:

`./mySystemStats`

The program accepts 6 Command Line Arguments, with their respective short forms:

`--system` or `-s`

<details>

  <summary>System Details </summary>

    To indicate that only the system usage should be generated
  
</details>

`--user` or `-u`

<details>

  <summary>User Details </summary>

    To indicate that only the users usage should be generated

</details>

`--graphics` or `-g`

<details>

  <summary>Graphics Details </summary>

    To include graphical output in the cases where a graphical outcome is possible as indicated below.
  
</details>

`--sequential` or `se`
<details>

  <summary>Sequential Details </summary>

    To indicate that the information will be output sequentially without needing to "refresh" the screen (useful if you would like to redirect the output into a file)
  
</details>

`--samples` or `-sa=`

<details>

  <summary>Samples Details </summary>

    If used the value N will indicate how many times the statistics are going to be collected and results will be average and reported based on the N number of repetitions.
    If not value is indicated the default value will be 10.
  

</details>

`--tdelay=` or `-td=`
<details>

  <summary>Tdelay Details </summary>

    To indicate how frequently to sample in seconds.
    If not value is indicated the default value will be 1 sec.
  
</details>


#
**Note: The program also accepts two positional integer arguments, where the first one represents the  number of samples and the second one is the tdelay. The program will use the default values if no arguments are provided. If only one integer argument is provided, it sets that value to the number of samples**
#

## Functions
`void get_stats(struct cpu_stat *cpu, int cpunum)`
 <details>

  <summary>get_stats Description </summary>

**Parameters:**

* `struct cpu_stat *cpu`: Pointer to a struct cpu_stat
* `int cpunum`: Number of the CPU to be used

**Return Value:**
* `void`

    This function is used to get the CPU usage statistics. It takes two arguments, the first one is a pointer to a struct cpu_stat and the second one is the number of the CPU to be used. It uses the `/proc/stat` file to get the relevant information. The function will return the CPU usage statistics in the struct cpu_stat.
</details>



`double calculate_load(struct cpu_stat *prev, struct cpu_stat *cur`
<details>
    <summary>calculate_load Description </summary>

**Parameters:**

* `struct cpu_stat *prev`: Pointer to the previous iteration's cpu stats
* `struct cpu_stat *cur`: Pointer to the current iteration's cpu stats

**Return Value:**
* `double`: CPU load in percentage

This function uses the following formula to calculate the CPU load:

    double load = (1000 * (total - idle) / total + 1) / 10;

Where the load is the CPU load in percentage.

</details>

`void starter(int samples, int tdelay)`
<details>
    <summary>starter Description </summary>

**Parameters:**

* `int samples`: Number of samples to be taken
* `int tdelay`: Time delay between samples

**Return Value:**
* `void`

This function is used as a header to the program. It prints the number of samples and the time delay between each sample. It uses the `<sys/resource.h>` library, and the `rusage` struct to get the system resources.
</details>

`void ender()`
<details>
    <summary>ender Description </summary>

**Parameters:**
* none

**Return Value:**
* `void`

This function is used as a footer to the program. It prints the system information. It uses the `<sys/utsname.h>` library, and the `utsname` struct to get the system information.

</details>

`void UserPrint()`
<details>
    <summary>UserPrint Description </summary>

**Parameters:**
* none

**Return Value:**
* `void`

This function is used to print the user information. It uses the `<utmp.h>` library, and the `utmp` struct to get the user information. It prints the user name, the terminal name, and the host name.
</details>

`double cpuPrint(char **graph_arr, int tdelay, int samples, double prev_load, bool graph, int i)`
<details>
    <summary>cpuPrint Description </summary>

**Parameters:**
* `char **graph_arr`: Pointer to a char array containing the graphical output for the CPU
* `int tdelay`: Time delay between samples
* `int samples`: Number of samples to be taken
* `double prev_load`: Previous CPU load
* `bool graph`: Boolean to indicate if graphical output is needed
* `int i`: Number of the current sample

**Return Value:**
* `double`: CPU load in percentage to be used in the next iteration

This function is used to print the CPU usage statistics. It uses `sysconf` to get the number of cores (iteraretes through the `/proc/stat` file to count the number of CPUs).
The function uses the `get_stats` function to get the CPU usage statistics. It then sleeps for the desired time delay. It then uses the `get_stats` function again to get the CPU usage statistics. It then uses the `calculate_load` function to calculate the CPU load. It then prints the CPU load in percentage.
If graphical is called, It prints two bars if the load is less than or equal to 0.01%, an additional one bar for every 0.1 increase upto 1, an additional one bar for every 1 increase upto 10, and an additional one bar for every 10 increase upto 100. As can be seen through this code: 

    if (prev_load > 0.01 && prev_load <= 1.00) {barCount = (int)(prev_load * 10); }
    else if (prev_load > 1.00 && prev_load <= 10.00) {barCount = prev_load + 10;}
    else if (prev_load > 10.00 && prev_load <= 100.00) {barCount = prev_load / 10 + 20;}

The function returns the CPU load in percentage to be used in the next iteration.
</details>

`double memoryPrint(char **memory_arr, int samples, bool graph, int i, double prev_virt)`
<details>
    <summary>memoryPrint Description </summary>

**Parameters:**
* `char **memory_arr`: Pointer to a char array containing the output for the memory
* `int samples`: Number of samples to be taken
* `bool graph`: Boolean to indicate if graphical output is needed
* `int i`: Number of the current sample
* `double prev_virt`: Previous virtual memory usage

**Return Value:**
* double: Virtual memory usage in GB to be used in the next iteration

This function is used to print the memory usage statistics. It uses the `<sys/sysinfo.h>` library, and the `sysinfo` struct to get the memory usage statistics.
It prints the virtual memory usage in GB. It calculates the memory usage using the following: 

    long long phys_mem = sys.totalram * sys.mem_unit;
    long long phys_used = (sys.totalram - sys.freeram) * sys.mem_unit;
    long long virt_mem = sys.totalram * sys.mem_unit + sys.totalswap * sys.mem_unit;
    long long virt_used = (sys.totalram - sys.freeram) * sys.mem_unit + (sys.totalswap - sys.freeswap) * sys.mem_unit;

Each of the variables are in bytes, which is why they are divided by 1024^3 to get the value in GB.

If the graphical output is needed, the function will print the graphical output for the memory. For every 0.01 GB change, the function will print an additional symbol based on whether the memory usage is increasing or decreasing.
The function returns the virtual memory usage in GB to be used in the next iteration.
</details>

`void finPrint(bool sys, bool user, bool graph, bool sequen, int samples, int tdelay)`
<details>
    <summary>finPrint Description </summary>

**Parameters:**
* `bool sys`: Boolean to indicate if system information is needed
* `bool user`: Boolean to indicate if user information is needed
* `bool graph`: Boolean to indicate if graphical output is needed
* `bool sequen`: Boolean to indicate if sequential output is needed
* `int samples`: Number of samples to be taken
* `int tdelay`: Time delay between samples

**Return Value:**
* `void`

This function is used to print the final output. It prints the starter() at the beginning and the ender() at the end. If sequential is false, it uses `printf("\033[H \033[2J \n");` to clear the screen.
It prints the CPU usage statistics and the memory usage statistics. Based on which flags are called.
It also prints the graphical output for the CPU and the memory if the graphical output is needed. If sequential is true, the screen is not cleared after each iteration, and the output is printed beneath the previous output.
It also declares and frees the memory for the char arrays that will be used to store the graphical output for the CPU and the memory.
</details>

`int main(int argc, char *argv[])`
<details>
    <summary>main Description </summary>

**Parameters:**
* `int argc`: Number of arguments
* `char *argv[]`: Array of arguments

**Return Value:**
* `int: 0`

This function is used to parse through the arguments passed to the program. It uses `strcmp()` to compare the arguments to the flags. It also uses `atoi()` to convert the arguments to integers if needed. The command line arguments are defined at the beginning of the usage page. It passes through the arguments and sets the boolean values to true if the flag is present. It also sets the number of samples and the time delay between samples if they are present. It then calls the `finPrint()` function to print the final output. If the user does not pass any arguments, the program will print the usage and system page.

**Note: If invalid arguments are passed, none of the commands are run, and the program will print:** 
    
        Please enter valid arguments

**However if at least one valid argument is passed, the program will run the valid arguments and ignore the invalid ones.**
</details>

#

## Examples + Additional Notes

Multiple arguments can be used at the same time. For example, the following command will generate the system usage statistics with graphical output and will take 5 samples with a time delay of 2 seconds between each sample:

`./mySystemStats -s -g -sa=5 -td=2`

The following command will generate the user usage statistics and will take 2 samples with a time delay of 8 seconds between each sample:

`./mySystemStats -u 2 8`




