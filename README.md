kssh
=============
python wrapper around ssh to distribute tasks across multiple machines by
splitting the input into blocks and assigning blocks to machine. the script
polls for completion of the task on a machine, and then assigns a new block.

reads in machine details from a JSON file with argument --machines:
```
[{
	"hostname": "default", 		# default settings, can be overridden per machine
	"username": "user",
	"connect_cmd": "ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -o ConnectTimeout=10 -o ConnectionAttempts=2",
	"poll_interval": 10,			# interval for polling for block complete
	"poll_timeout": 2,				# initial timeout for polling ssh session
	"upload_timeout": 2,			# initial timeout for the block upload ssh session
	"download_timeout": 2,		# initial timeout for the output download ssh session
	"init_timeout": 2					# timeout for the initialization steps
},
{
	"hostname": "worker1.example.com", # per machine settings
	"wd": "~/working_dir",
	"init_timeout": 10
},
	...
]
```
timeout values are adjusted as the script runs using a weighted moving average.
however, the closer the initial settings are to optimal, the quicker we will
converge.

depending upon whether you specify multiple files or a single file as --input,
the script will either distribute blocks as lines in the single input file or
as whole files. the number of lines per block can be specified with --blocksize.

you can specify an initialization file to be copied to the remote machines and an
initialization script using --init-file and --init-script, respectively. The file
will be copied first then the script will be run.

for each assigned block, the parameter --task will be executed with the block as
stdin. stdout and stderr will be returned from the remote machine. The script
expects the task to return 0 on success. That can be changed with --task-success-code.

as a simple example:
```
./kssh --input list_of_urls --blocksize 100 --task "xargs -P 5 curl"
```
will size 100 urls per block to each machine and run 5 curls in parallel
per machine. the output of curl will be written to stdout.
