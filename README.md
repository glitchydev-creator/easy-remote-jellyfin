# easy-remote-jellyfin
This project lays out how to get a jellyfin server to the internet easily with limited knowledge

This will require a VPS or a server that can reach a public IP, and Duckdns. For this I chose IONOS because it was $2/month. There may be ways to do this for free, but this is still mostly free. 

You will find in here the steps on how to get your jellyfin in docker. Get jellyfin with tailscale and point it out to the internet securely without opening any ports. You will need to know some coding to get this going, but it is all easily available to find here or online. 

1. The first Step will to be what distro to run your server on. In this case I choose Debian 12. You can choose whatever system you want. Your steps for installation may vary

2. Once you install your os you will install UFW, Docker compose, Docker and OpenSSH to access the remote VPS server.

3. Perform the similar tasks on the VPS server

4. On your primary non VPS server you will install tailscale and nginx proxy manager in docker. You will have to create an account which is free. Once you create the tailscale account you can download and run it on your system

5. Follow the instructions per your Linux distro to install tailscale.
6. https://tailscale.com/kb/1174/install-debian-bookworm

* curl -fsSL https://pkgs.tailscale.com/stable/debian/bookworm.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
* curl -fsSL https://pkgs.tailscale.com/stable/debian/bookworm.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
* sudo apt-get update
* sudo apt-get install tailscale
*  sudo tailscale up
- Follow the instructions to authenticate your machine

tailscale ip -4  
save this IP for later you will use this to route your jellyfin on 8096 to your VPS

8. once it is installed you can check your ip with tailscale ip -4  that will give you the IP that you need. You can keep that for future reference

9. Then you run jellyfin with docker compose on your local server. Use the included Docker compose or modify for your machine. This will run on port 8096 on your local box.

10. On the server install tailscale and docker compose follow the instructions to download and install docker compose on your OS. I will provide how to on Debian 12 as of this writing.
Perform these tasks on thhe VPS server 
Add Docker’s GPG Key:

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

Add the Docker Repository:

echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

Update Package Index Again:

sudo apt update

Install Docker Engine:

sudo apt install docker-ce -y

Add the Docker Compose Plugin:

sudo apt install docker-compose-plugin -y

Verify Installation:

docker compose version

11. Perform the tailscale install similar to before. You will be able to monitor both machines from your tailscale machine admin page

12. Install nginx proxy manager in Docker compose on the VPS machine. This will be used to handle the certificates and provide hardening for exploits of the site.
13. Use the provided docker compose yaml. You will not need to modify this. I only suggest you make a new directory in /srv
14. sudo mkdir /srv/nginx-proxy manager
15. copy the yaml to this location
16. Next run Sudo apt install nginx certbot python3-certbot-nginx. Make sure it is not running
17. sudo systemctl stop nginx after the install. This creates nginx.conf which proxymanager relies on. 
18. Then run sudo docker compose up -d from the /srv/nginx-proxymanager location this will run the container in the background.

19. Now back to your primary server or media station

20. The next step will be to create a jellyfin docker for this it will be similar to how you did previous steps

21. sudo mkdir /srv/jellyfin
22. Then copy the docker-compose.yml for jellyfin here
23. then sudo docker compose up -d  this will run jellyfin in the background on port 8096
24. browse to localhost:8096 and setup jellyfin for the first time 

25. Now with tailscale nginx and jellyfin all running you can create a duckdns account for free
26. When that is done you will create your subdomain which will be yourdomain.duckdns.org change the yourdomain to whatever you want. This will be the pubilc URL of your machine

27. From your local machine you can browse to the NGINX proxy manager webpage this works thanks to tailscale.
28. you can get the ip of your vps from your tailscale admin
29. Then browse to your tailscale ip 100.X.X.X:81 port 81 is used for the proxymanager

30. From here you will setup your site
31. you will login with the default@example.com and changeme password
32. put in your email and password

33. From here you will put in your duckdns domian in the proxy forwader host. yourdomain.duckdns.org
34. Then for the ip you will use the Tailscale ip of your local server 100.X.X.X:8096 this will be the port that jellyfin uses
35. Make sure to select common exploit protection

36. Next you can update the ip4 address in your duckdns to the public IP of your VPS server. This will show up in your admin console. YOu will put in the IP and select update this will make it so your domain can be accessed on the internet

37. Once you have that done you can go back to the nginx proxy manager. From here you will setup the SSL certificate
38. you will go to the SSL certificates
39. Then put in your domin. Yourdomain.duckdns.org
40. Then select the DNS challenge
41. Select duckdns and put in your token
42. Then agree to the terms.
43. Once the certificate is created go back to the proxy host.
44. Select edit and apply the duckdns Certificate you created.
45. Select force SSL and enable http/2 support

46. When all that is done you can confirm your site is working by going to yourdomain.duckdns.org

47. When it is all done and working yell thank you! Hopefully it worked. I created this out of frustration from many other writeups on this subject and created my own. 
