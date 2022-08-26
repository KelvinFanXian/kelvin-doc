# examples

```bash
length($0) > 72
Print lines longer than 72 characters.
{ print $2, $1 }
Print first two fields in opposite order.

BEGIN { FS = ",[ \t]*|[ \t]+" }
      { print $2, $1 }

       Same, with input fields separated by comma and/or spaces and tabs.

     { s += $1 }
END  { print "sum is", s, " average is", s/NR }

       Add up first column, print sum and average.
       /start/, /stop/
       Print all lines between start/stop pairs.

BEGIN     {    # Simulate echo(1)
     for (i = 1; i < ARGC; i++) printf "%s ", ARGV[i]
     printf "\n"
     exit }
```

