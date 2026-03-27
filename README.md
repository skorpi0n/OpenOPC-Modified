# OpenOPC-Modified
A modified version of the original OpenOPC by Barry Barnreiter (https://openopc.sourceforge.net/) but with stdout flush after each read and real exit codes

# What has changed

## Flushing
Previously, the opc.py never flushed manually, causing it to only flush when reaching a treshold of 4kb when running in a subprocess (not from commandline)

To solve this, I replaced the following row
```
write = sys.stdout.write
```
With this
```
def write(s):
    sys.stdout.write(s)
```
I also added this code after the for-loop:
```
sys.stdout.flush()
```


If it is preferred to flush after each row instead, this row can be put after "sys.stdout.write(s)" like this:
```
def write(s):
    sys.stdout.write(s)
    sys.stdout.flush()
```
## Retry-backoff
A retry-backoff has been added whenever a communcation error is detected
1 or less than 5 errors: 10 seconds backoff
5 or less than 20 erors: 60 seconds backoff
above 20 errors: 300 seconds backoff

## Non-blocking-while loop
The while-loop for reading opc-tags repeatedly with argument "-L" has been improved to run every second but only do the actual reading every nth loop.
This was necessary for the ability to implement "stop-flag-file" functionality described below

## Additional stop-handling by "stop-flag-file"
An ability to break the while-loop from external processes has been implemented by simply reading if a specific file exists
The while loop will check if the file "opc.py.stop.flag" exists in the running directory (where you called opc.py from)
If it detects this file, it will remove it and break the loop so that any OPC-DA connections can be disconnected gracefully.
NOTE! If the file "opc.py.stop.flag" is detected upon script start, it will be removed.

## Exit codes
Previously, the opc.py had all the same exit-codes of 0 (no error) when using:
```
exit()
```
Now, they are changed to sys.exit(0) or sys.exit(1) to exit with real exit-code that can be caught accordingly with a subprocess handler
