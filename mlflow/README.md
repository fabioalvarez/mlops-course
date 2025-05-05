## Check what pid is running the server
### Find the process using port 5000
lsof -i :5000

### Kill the process (replace <PID> with the process ID from the above command)
kill -9 <PID>