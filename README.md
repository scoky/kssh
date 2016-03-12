# kssh
python wrapper around ssh to distribute tasks across multiple machines

reads in machine details from a JSON file. See .machines for an example.

distributes either lines of a file or files to the machines as input.

user can specify the bash commands to run on the remote machines.

kssh will upload the input to the remote machines, load balance across 
the machines, poll the machines for completion, and fetch the output.
