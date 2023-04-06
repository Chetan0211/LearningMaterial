# How to Kill Port in MAC:

Need to get the PID for the port you need to kill.

```bash
> lsof -ti :3000 (where 3000 is your current port in use)
```

That will return you id if it something is running in that port.

Once you get that we can kill it using:

```bash
> kill -QUIT <PID> (here <PID> is the number we got)
```