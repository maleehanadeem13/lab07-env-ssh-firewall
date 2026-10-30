# Lab 07 Challenge: Environment Audit and Ping Block
Student: Maleeha Nadeem (FA24B1-SE-013)

## Job 1: PATH, LANG and PWD
Commands:
    printenv | grep ^PATH
    printenv | grep ^LANG
    printenv | grep ^PWD
Values:
- PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/maleehanadeem13
- LANG=en_US.UTF-8
- PWD=/home/maleehanadeem13/lab07-env-ssh-firewall

## Job 2: Temporary STUDENT_ variables
Commands:
    export STUDENT_NAME="Maleeha Nadeem"
    export STUDENT_ROLL_NUMBER="FA24B1-SE-013"
    export STUDENT_SEMESTER="5"
    printenv | grep '^STUDENT_'
Answer: All three were shown. After exit and a new SSH login,
printenv | grep '^STUDENT_' printed nothing, so they were gone.

## Job 3: Permanent STUDENT_ variables
Commands:
    cat >> ~/.bashrc  (added the three export lines at the end)
    exit, then ssh maleehanadeem13@192.168.148.132
    printenv | grep '^STUDENT_'
Answer: After the new login all three variables were still there,
because ~/.bashrc is read at every login.

## Job 4: Block ping, prove it, allow it again
Commands:
    ping -n 4 192.168.148.132              (Git Bash: Reply from ...)
    sudo cp /etc/ufw/before.rules ~/before.rules.backup
    sudo sed -i '37s/-j ACCEPT/-j DROP/' /etc/ufw/before.rules
    sudo ufw reload
    ping -n 4 192.168.148.132              (Request timed out, 100% loss)
    sudo iptables -S ufw-before-input | grep "icmp-type 8"   (ends with -j DROP)
    sudo sed -i '37s/-j DROP/-j ACCEPT/' /etc/ufw/before.rules
    sudo ufw reload
    ping -n 4 192.168.148.132              (Reply from ... again)
Answer: Line 37 of /etc/ufw/before.rules is the incoming echo-request rule.
Changing ACCEPT to DROP blocked ping; changing it back allowed ping again.
Note: right after blocking, ping still replied for about 30 seconds, because
the firewall treated the repeated pings as an already established connection.
