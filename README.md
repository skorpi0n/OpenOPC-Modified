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

## Exit codes
Previously, the opc.py had all the same exit-codes of 0 (no error) when using:
```
exit()
```
Now, they are changed to exit(0) or exit(1) to exit with real exit-code that can be caught accordingly with a subprocess handler
