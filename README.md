## Theory [2]

* [0.4] What are [computer ports](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) on a high level? How many ports are there on a typical computer?

A port is a number assigned to uniquely identify a connection endpoint and to direct data to a specific service. At the software level, within an operating system, a port is a logical construct that identifies a specific process or a type of network service. A port is identified for each transport protocol and address combination by a 16-bit unsigned number, known as the port number.  
There are 65535 port on the typical computer. 

* [0.4] What is the difference between http, https, ssh, and other protocols? In what sense are they similar? Name default ports for several data transfer protocols.

Most websites use Hypertext Transfer Protocol (HTTP) to transfer information from the client to the server and back again. Secure Hypertext Transfer Protocol (HTTPS) is used to transfer information securely, while Secure Shell (SSH) is an alternative method of transferring information and is a network protocol to use between two cases of communication. It is commonly used for remote server access and management, and for detecting file transfers between applications.

Protocols are similar in their original purpose to transfer information. The difference between lies in encryption and specificity of their tasks. 

Default ports: The most common protocols use TCP/IP, which uses the port number 80 for HTTP, and port 443 for HTTPS. The other most common port is SSH, which use port 22.

* [0.4] Explain briefly: (1) what is IP, (2) what IPs are called 'white'/public, (3) and what happens when you enter 'google.com' into the web browser.
1) An Internet Protocol address (IP address) is a label that is connected to a computer network that uses the Internet Protocol for communication. An IP address serves two main functions: network interface identification and location addressing.

2) A white IP is an IP address that can be accessed directly over the internet and is assigned to your network router by your internet service provider.

3) The first thing that happens is that your browser looks up in its cache to see if that website was visited before and the IP address is known. If it can't find the IP address then it asks your operating system to locate the website. Then the OS will make a DNS request to find the IP Address of the web page. Then it will have at least one of the authoritative name servers associated with that URL, and after going to the Name Server, it will return the IP Address associated with your URL. After the OS has the IP Address and gives it to the browser, it then makes a GET (a type of HTTP Method) to said IP Address. And upon receiving the request the server sends a response with the IP Address of the chosen server along with the SSL (Secure Sockets Layer) certificate to initiate a secure session (HTTPS). Finally, the chosen server then sends the HTML, CSS, and Javascript files back to the OS who in turn gives it to the browser to interpret it.

* [0.4] What is Nginx? How does it work on the high level? List several alternative web servers.

Nginx is an open-source web server as a web server, is now also used as a reverse proxy, HTTP cache, and load balancer, an HTTP and reverse proxy server, a mail proxy server.

On high level, Nginx works by taking incoming HTTP requests from clients and redirecting them to the backend server. The internal server then requests and returns an appropriate response, which Nginx sends back to the client.

Nginx can be used as a standalone web server or as a reverse proxy.
Alternatives - Lighttpd, OpenLiteSpeed, Caddy

* [0.4] What is SSH, and for what is it typically used? Explain two ways to authenticate in an SSH server in detail.
SSH - is a cryptographic network protocol for operating network services securely over an unsecured network.
It is commonly used for remote access to and management of servers, and for secure file transfers between systems.

1 way-password-based authentication
In password-based authentication, after establishing secure connection with remote servers, SSH users pass on their usernames and passwords to remote servers for client authentication. These credentials are shared through the secure tunnel established by symmetric encryption. The server checks for these credentials in the database and, if found, authenticates the client and allows it to communicate. 

2 way - public key-based authentication
After the client establishes a connection with the remote server, the client informs the server of the key pair it would like to authenticate itself with. The server verifies the existence of this key pair in its database and then sends an encrypted message to the client. The client decrypts the message with it’s private key and generates a hash value which is sent back to the server for verification. The server generates its own hash value and compares it with the one sent from the client. When both the hash values match, the server is convinced of the client’s authenticity and allows it to communicate with the server.


For the second homework i also used yandex cloud ubuntu virtual machine 

Keys were generated by using ssh-keygen command

ATAC эксперимент https://www.encodeproject.org/files/ENCFF754GUE/
CHIP-Seq Transcription factors: https://www.encodeproject.org/files/ENCFF209UEC/, https://www.encodeproject.org/files/ENCFF241LPZ/, https://www.encodeproject.org/files/ENCFF521UKW/

```
sudo apt-get install wget samtools tabix
mkdir genome 
cd genome
wget ftp://ftp.ensembl.org/pub/release-99/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
gunzip Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
samtools faidx Homo_sapiens.GRCh38.dna.primary_assembly.fa

wget ftp://ftp.ensembl.org/pub/release-99/gff3/homo_sapiens/Homo_sapiens.GRCh38.99.gff3.gz
gunzip Homo_sapiens.GRCh38.99.gff3.gz
sort -k1,1 -k4,4n Homo_sapiens.GRCh38.99.gff3 > Homo_sapiens.GRCh38.99.sorted.gff3
bgzip Homo_sapiens.GRCh38.99.sorted.gff3
tabix -p gff Homo_sapiens.GRCh38.99.sorted.gff3.gz

mkdir experiment
cd experiment 
wget https://www.encodeproject.org/files/ENCFF209UEC/@@download/ENCFF209UEC.bed.gz -O tf1.bed.gz
wget https://www.encodeproject.org/files/ENCFF241LPZ/@@download/ENCFF241LPZ.bed.gz -O tf2.bed.gz
wget https://www.encodeproject.org/files/ENCFF521UKW/@@download/ENCFF521UKW.bed.gz -O tf3.bed.gz
wget https://www.encodeproject.org/files/ENCFF754GUE/@@download/ENCFF754GUE.bed.gz -O atac.bed.gz

gunzip *.bed.gz
sort -k1,1 -k2,2n  tf1.bed > tf1_sorted.bed
sort -k1,1 -k2,2n  tf2.bed > tf2_sorted.bed
sort -k1,1 -k2,2n  tf3.bed > tf3_sorted.bed
sort -k1,1 -k2,2n  atac.bed > atac_sorted.bed

bgzip atac_sorted.bed
bgzip tf1_sorted.bed
bgzip tf2_sorted.bed
bgzip tf3_sorted.bed

tabix -p bed tf1_sorted.bed.gz
tabix -p bed tf2_sorted.bed.gz
tabix -p bed tf3_sorted.bed.gz
tabix -p bed atac_sorted.bed.gz
```

# JBrowse
```
sudo apt install npm
sudo npm install -g @jbrowse/cli

cd /..
cd mnt
sudo mkdir JBrowse
sudo jbrowse create JBrowse

sudo apt-get install -y nginx
sudo nano /etc/nginx/nginx.conf

sudo jbrowse add-assembly /home/sanyaruss/genome/Homo_sapiens.GRCh38.dna.primary_assembly.fa --load copy --out /mnt/JBrowse/
sudo jbrowse add-track /home/sanyaruss/genome/Homo_sapiens.GRCh38.99.sorted.gff3.gz  --load copy --out /mnt/JBrowse/

cd ~
cd genome
cd experiment
gunzip tf1_sorted.bed.gz 
awk '{gsub(/^chr/,""); print}' tf1_sorted.bed > $(echo tf1_sorted.bed| cut -d '.' -f 1)'_renamed.bed'
bgzip tf1_sorted_renamed.bed
tabix -f tf1_sorted_renamed.bed.gz

gunzip tf2_sorted.bed.gz 
awk '{gsub(/^chr/,""); print}' tf2_sorted.bed > $(echo tf2_sorted.bed| cut -d '.' -f 1)'_renamed.bed'
bgzip tf2_sorted_renamed.bed
tabix -f tf2_sorted_renamed.bed.gz

gunzip tf3_sorted.bed.gz 
awk '{gsub(/^chr/,""); print}' tf3_sorted.bed > $(echo tf3_sorted.bed| cut -d '.' -f 1)'_renamed.bed'
bgzip tf3_sorted_renamed.bed
tabix -f tf3_sorted_renamed.bed.gz

gunzip atac_sorted.bed.gz 
awk '{gsub(/^chr/,""); print}' atac_sorted.bed > $(echo atac_sorted.bed| cut -d '.' -f 1)'_renamed.bed'
bgzip atac_sorted_renamed.bed
tabix -f atac_sorted_renamed.bed.gz

sudo jbrowse add-track tf1_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/
sudo jbrowse add-track tf2_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/
sudo jbrowse add-track tf3_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/
sudo jbrowse add-track atac_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/

sudo nginx -s reload 
```
session link - http://158.160.13.136/jbrowse/?session=share-2PsOr6LQT1&password=OgA8e

