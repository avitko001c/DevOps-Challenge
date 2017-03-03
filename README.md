# DevOps-Challenge
Project for the Comcast DevOps Challenge

## Ansible role `httpd`

An Ansible role for installing and configuring the Apache web server with SSL with a default webpage.

- Install all the packages;
- Install the main configuration file and index.html;
- Install the configuration file for `mod_ssl`.
- Install support PHP

HTTPS/TLS is enabled by default and is using the standard self-signed certificate. Your own certificates can be used by setting the appropriate role variables in role/httpd/vars/RedHat.yml.

Currently, no virtual hosts or other features are provided.

## Requirements

- The firewall settings are managed by the security role along with SELinux.
- If you want to use custom certificates, you have to make sure that they are installed on the system before applying this role and you can edit the variables to point to the certificate locations.

## Role Variables

If no variables are set, applying this role will result in a configuration equivalent to the default install. Consequently, no variables are required.

| Variable                        | Default                                    | Comments (type)                                                                       |
| :---                            | :---                                       | :---                                                                                  |
| `selinux_state`           	  | disabled  		                       |                                                                                       |
| `selinux_type`           	  | targeted  		                       |                                                                                       |
| `firewall_allow_services`       | 'ssh, http, https'	                       |                                                                                       |
| `httpd_AccessLog_ssl`           | logs/ssl_access_log                        |                                                                                       |
| `httpd_DocumentRoot`            | '/var/www/html'                            |                                                                                       |
| `httpd_ErrorLog_ssl`            | logs/ssl_error_log                         |                                                                                       |
| `httpd_ErrorLog`                | logs/error_log                             |                                                                                       |
| `httpd_Listen_ssl`              | 443                                        |                                                                                       |
| `httpd_Listen`                  | 80                                         |                                                                                       |
| `httpd_LogLevel_ssl`            | warn                                       |                                                                                       |
| `httpd_LogLevel`                | warn                                       |                                                                                       |
| `httpd_SSLCACertificateFile`    | -                                          |                                                                                       |
| `httpd_SSLCertificateChainFile` | -                                          |                                                                                       |
| `httpd_SSLCertificateFile`      | /etc/pki/tls/certs/localhost.crt           |                                                                                       |
| `httpd_SSLCertificateKeyFile`   | /etc/pki/tls/private/localhost.key         |                                                                                       |
| `httpd_SSLCipherSuite`          | See [default variables](defaults/main.yml) |                                                                                       |
| `httpd_SSLHonorCipherOrder`     | 'on'                                       |                                                                                       |
| `httpd_SSLProtocol`             | 'all -SSLv3 -TLSv1'                        |                                                                                       |
| `httpd_ServerAdmin`             | root@localhost                             |                                                                                       |
| `httpd_ServerRoot`              | '/etc/httpd'                               |                                                                                       |
| `httpd_ServerTokens`            | Prod                                       | See [documentation](https://httpd.apache.org/docs/current/mod/core.html#servertokens) |
| `httpd_scripting`               | 'none'                                     | Allowed values: `php`                                                                 |

## Example Playbook

RedHat/CentOS 7 Playbook

- hosts: webservers
  become: true
  vars_files:
    - vars/main.yml
  roles:
    - httpd


## Currently this role is for EL7 however with minimal tweaking could be used for EL6.

RedHat/CentOS 6 Playbook

- hosts: all
  become: true
  vars_files:
    - vars/main.yml
  roles:
    - httpd
  post_tasks:
    - name: Ensure firewall is running, and open ports 80, 443, and 22
      service:
        name: iptables
        state: started
    - name: Open 80 port for firewalld
      command: sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    - name: Open 443 port for firewalld
      command: sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
    - name: Open ssh port for firewalld
      command: sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

## License

GNU Public License 3.0, see [LICENSE.md]

## Contributors

- [Andrew Vitko](https://github.com/avitko/) (maintainer)
- [Bert Van Vreckem](https://github.com/bertvv/) (used security ideas here)
