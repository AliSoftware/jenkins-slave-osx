# Jenkins Slave for OS X

Scripts to create and run a [Jenkins](http://jenkins-ci.org) slave via [Jave Web Start](https://wiki.jenkins-ci.org/display/JENKINS/Distributed+builds#Distributedbuilds-LaunchslaveagentviaJavaWebStart) (JNLP) on OS X as a Launch Daemon.



## Quick Start
`bash <( curl -L https://raw.github.com/rhwood/jenkins-slave-osx/master/install.sh )`



## Features
OS X slaves created with this script:
* Starts on system boot
* Runs as an independent user
* Uses a Java Truststore for self-signed certificates (so your Jenkins master can use a self-signed certificate, and you do not have to instruct the slave to trust all certificates regardless of source)
* Uses the OS X Keychain for secrets



## Install
`bash <( curl -L https://raw.github.com/rhwood/jenkins-slave-osx/master/install.sh ) [options]`

The install script has the following options:
* `--master-cert=/path/to/cert.cer` to install either a self-signed certificate for the Jenkins master, or the root certificate of the CA that signed the Jenkins master certificate. *Optional;* the installer prompts for this as needed.
* `--java-args="ARGS"` to specify any optional java arguments. *Optional;* the installer does not test these arguments.
* `--master=URL` to specify the Jenkins Master on the command line. *Optional;* the installer prompts for this if not specified on the command line.
* `--node=NAME` to specify the Slave's node name. *Optional;* this defaults to the OS X hostname and is verified by the installer.
* `--user=NAME` to specify the Jenkins user who authenticates the slave. *Optional;* this defaults to your username on the OS X slave and is verified by the installer.



## Update
Simply rerun the installer. It will reinstall the scripts, but use existing configuration settings.



## Configuration
The following file in ``/var/lib/jenkins`` (assuming you installed this service in the default location) can be used to configure this service:
``Library/Preferences/org.jenkins-ci.slave.jnlp.conf``



## Adding Server Certificates
If you decide to secure the Jenkins master, or need to add additional certificates for the slave to trust the Jenkins master, you only need (assuming your service account is "jenkins", and your CA is StartSSL.com) from a command line:

1. `sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.slave.jnlp.plist`
2. `sudo -i -u jenkins`
3. `curl -O http://www.startssl.com/certs/ca.crt`
4. `./security.sh add-java-certificate --authority --alias=root-ca --certificate=./ca.crt`
5. `curl -O http://www.startssl.com/certs/sub.class1.server.ca.crt`
6. `./security.sh add-java-certificate --alias=ca-server --certificate=./sub.class1.server.ca.crt`
7. `rm ./*ca.crt`
8. `exit`
9. `sudo launchctl load /Library/LaunchDaemons/org.jenkins-ci.slave.jnlp.plist`
