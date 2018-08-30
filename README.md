# Setup the server

```
sudo apt-get update
sudo apt-get upgrade

# ssh
sudo service ssh start
sudo service ssh status

# docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
sudo usermod -aG docker <your-user>

# docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# chroot
sudo setcap cap_sys_chroot+ep $(which chroot)

# git-shell
sudo adduser git
sudo usermod -aG adm git
sudo usermod -aG docker git
sudo sh -c 'which git-shell >> /etc/shells'
sudo chsh git -s $(which git-shell)
su - git --shell /bin/bash
mkdir .ssh && chmod 700 .ssh
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
logout

# /repos
sudo mkdir -p /var/opt/git
sudo chown -R git /var/opt/git
sudo ln -s /var/opt/git /repos
sudo chown -R git /repos
sudo sh -c 'echo "GIT_BASE_DIR=/repos" >> /etc/environment'

# nginx-proxy
docker network create nginx-proxy_net
docker run --name nginx-proxy --net nginx-proxy_net -d -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock:ro jwilder/nginx-proxy:alpine
```

# Set DNS on localhost (OSX)

```
brew install dnsmasq
mkdir -p /usr/local/etc
echo "address=/.mini/10.0.1.64" > /usr/local/etc/dnsmasq.conf
sudo cp -fv /usr/local/opt/dnsmasq/*.plist /Library/LaunchDaemons
sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist
sudo mkdir -p /etc/resolver
sudo sh -c 'echo "nameserver 127.0.0.1" > /etc/resolver/mini'
```
