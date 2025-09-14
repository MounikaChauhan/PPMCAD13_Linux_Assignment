Linux Assignment : (for screenshots please refer to document)

Commands to perform the task: 

1.	Install and configure monitoring tools (htop or nmon)  to monitor CPU, memory, and process usage.
Commands to install : 
	sudo yum install epel-release -y
	sudo yum install htop -y
  sudo yum install nmon -y

3.	Disk usage monitoring
Commands to save the disk usage/directory usage into different files and monitor the usage.
df -h > /var/log/disk_usage_df.log
du -sh /home/* > /var/log/disk_usage_du.log

3.	Identifying the CPU usage processor.
ps aux --sort=-%cpu | head -n 10 > /var/log/top_cpu_processes.log

ps aux command used to list the process running
--sort is performing the sort on based on percentage
Head –n 10 will return top 10 process.

The results stored into the top_cpu_processes.log file.
Command : ps aux --sort=-%mem | head -n 10 > /var/log/top_mem_processes.log

Similar to the cpu command, now replacing the cpu with mem to get the top 10 memory usage processes.
Step 4: automate the above process, such that any developer working on the machine would notify.

Add the above commands to crontab 

0 * * * * htop -b -n 1 > /var/log/htop_hourly.log
0 * * * * df -h > /var/log/disk_usage_df_hourly.log
0 * * * * du -sh /home/* > /var/log/disk_usage_du_hourly.log
0 * * * * ps aux --sort=-%cpu | head -n 10 > /var/log/top_cpu_processes_hourly.log
0 * * * * ps aux --sort=-%mem | head -n 10 > /var/log/top_mem_processes_hourly.log
0 * * * *  => Run the command at minute 0 of every hour. So it triggers at 12:00, 1:00, 2:00, ..., all the way to 23:00

Task 2: User Management and Access Control

For the user management, create our 2 developers, Sarah and Mike.
  command to create a user  : sudo useradd <username>
  command to create password: sudo passwd <username>
  command to create the directory : mkdir –p /home/<username>/workspace
    This creates the workspace directory in respective users account.

Update the permission for restricted access.
  sudo chown <username>:<username> /home/<username>/workspace
  sudo chmod 700 /home/<username>/workspace

Step 3: Enforce Password Policy
Install libpwquality for complexity enforcement

configure the password complexity, 
Enforces at least one digit, uppercase, lowercase, and special character.
minlen = 12
dcredit = -1
ucredit = -1
ocredit = -1
lcredit = -1
 

Once the complexity is set, now we can set the password expiration time.
command to set : sudo chage –M 30 <username>

check the info on password change with below command.
sudo chage –l <username>
As shown below, password expiry has been set to Oct 14, which is 30 days from today.


Challenges 1: 
Unable to ssh using different user. Initially I have configured to use the private network and ip to use 10.9.5.5 , this didn’t let me connect from different user as my VirtualBox is using NAT, which isolates the VM from your host unless port forwarding is explicitly set up.
So:
•	10.9.5.5 is not reachable from your host unless bridged or forwarded.
•	Port 22 is not exposed to your host for direct SSH access to mike@10.9.5.5.

This can be fixed either by allowing the port forwarding to the specific IP or allow the public network configuration i.e., bridged network.
I have choosen option 2, and was able to connect.

Task 3: Backup Configuration for Web Servers

Login with user mike and install nginx, similarly do the same for Sarah and install apache

Commands for install nginx : 
sudo yum install -y epel-release
sudo yum install -y nginx
 
Enable nginx

commands : sudo systemctl start nginx
 

ensure that configuration are /usr/share/nginx/html. Similarly, install the apache.

Challenge 2: Unable to run apache from Sarah account, as nginx is already listening in port 80
confirm which process is running in the port 80
Update the port in apache from 80 to 8080

access apache using <VM IP>:8080

Once these are running backup script to be added to crontab, verify the logs from the /backups folder created 
 
