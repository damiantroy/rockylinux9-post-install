# Rocky Linux 9 Post Install

This document is for the basic setup steps after doing a 'Minimal Install' of Rocky Linux 9.

1. Copy your SSH key:

    From your workstation, copy your SSH key and log into your new system:
    
    ```shell script
    ssh-copy-id <my_user>@<my_server>
    ssh <my_user>@<my_server>
    ```
   
    Then if you're using SSH keys, you can disable password authentication:
    
    ```shell script
    sudo sed -i 's/\(#\)\{0,1\}PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
    sudo systemctl restart sshd
    ```
   
1. Update your system:

    ```shell script
    sudo dnf update
    sudo reboot
    ```
   
1. Enable automatic updates:

    Only if you want automatic upadates.

    ```shell script
    sudo dnf install dnf-automatic
    sudo sed -i 's/^apply_updates.*/apply_updates = yes/' /etc/dnf/automatic.conf
    sudo systemctl enable --now dnf-automatic.timer
    sudo systemctl enable --now dnf-automatic-install.timer
    ```
   
1. Configure sshd:

    These sshd settings are mostly personal preference, so have a close look before applying, especially
    the `AllowUsers` line, as you'll need to put in your username.
    
    ```shell script
    echo 'Authorised uses only. All activity may be monitored and reported.' | sudo tee /etc/issue.net
    echo 'AllowUsers <my_user>' | sudo tee -a /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}AddressFamily.*/AddressFamily inet/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}LoginGraceTime.*/LoginGraceTime 60/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}MaxAuthTries.*/MaxAuthTries 4/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}MaxSessions.*/MaxSessions 4/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}HostbasedAuthentication.*/HostbasedAuthentication no/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}IgnoreRhosts.*/IgnoreRhosts yes/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}PermitEmptyPasswords.*/PermitEmptyPasswords no/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}PermitUserEnvironment.*/PermitUserEnvironment no/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}MaxStartups.*/MaxStartups 10:30:60/' /etc/ssh/sshd_config
    sudo sed -i 's/\(#\)\{0,1\}Banner.*/Banner \/etc\/issue.net/' /etc/ssh/sshd_config
    sudo systemctl restart sshd
    ```
