## Theory [2]

[0.5] What is Docker, and how it differs from dependencies management systems? From virtual machines?

Docker - a platform that enables developers to build, run, update and manage containers (standardized, executable components that combine application source code with the operating system (OS) libraries and dependencies required to run that code in any environment).

Docker differs from other dependency management systems by its security, free fee for charge, simplicity and flexibility. 

VMs have the host and guest operating systems inside each VM. In contrast, Docker containers host on a single physical server with a host operating system, which spreads among them. 
A VM usually takes minutes to start, whereas Docker containers take seconds, so it's much faster.
A VM gets its own set of resources allocated to it, and does minimal sharing. You get more isolation, but it is much heavier (requires more resources). With Docker you get less isolation, but the containers are lightweight (require fewer resources). 

[0.5] What are the advantages and disadvantages of using containers over other approaches?
Advantages: 
* require less system resources
* containers can be deployed easily to multiple different operating systems
* better speed
* simplicity 
* security 

Disadvantages: 
* container can go down
* specific security risk 
* missing features -> slowly updated 

[0.5] Explain how Docker works: what are Dockerfiles, how are containers created, and how are they run and destroyed?

A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

1 step. Set up docker on computer
2 step. Create a blank text file named Dockerfile
3 step. RUN apt-get update
4 step. cd directory - move to directory with dockerfile 
5 step. docker build -t username/imagename:tag .
6 step. docker run IMAGE - to create container
7 step. docker kill - to destroy it 

[0.25] Name and describe at least one Docker competitor (i.e., a tool based on the same containerization technology).

Containerd is a container runtime project that has been Docker's preferred container runtime for years. Containerd handles all aspects of container lifecycle management for its host system.

It creates, runs, and destroys containers as needed. It also handles image transfers and storage, container supervision, as well as low-level storage and network attachments.

[0.25] What is conda? How it differs from apt, yarn, and others?

Conda is an open source package management system and environment management system. Conda installs, runs and updates packages and their dependencies. 

Conda is binary package manager with support for environments, that's why users can use for example various python versions in different env.


As my computer didn't deal well with conda installation all steps in 1 homework were processed in Yandex Cloud VM from the 2 homework.

```
wget https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh
bash ~/Anaconda3-2022.05-Linux-x86_64.sh
export PATH=/home/sanyaruss/anaconda3/bin:$PATH
    
conda create -n hw python=3.9 --no-default-packages
conda activate hw
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
conda install -y bedtools=2.30.0
conda install -y salmon=1.9.0
conda install -y multiqc=1.13
conda install -y samtools=1.16.1
conda install -y star=2.7.10b
conda install -y fastqc=0.11.9
conda install -y picard

conda env export > hw.yml
```

# Docker work

```
sudo apt install docker.io
sudo snap install docker
mkdir Docker
cd Docker
touch Dockerfile
vim Dockerfile
```

```
FROM ubuntu:20.04

RUN apt-get update
RUN apt -y install apt-utils apt-transport-https openjdk-11-jre-headless unzip python3-pip

RUN touch /.bashrc

RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.9.zip && \
    unzip fastqc_v0.11.9.zip && \
    rm fastqc_v0.11.9.zip && \
    chmod a+x FastQC/fastqc && \
    echo 'alias fastqc="/FastQC/fastqc"' >> /.bashrc

RUN wget https://github.com/alexdobin/STAR/releases/download/2.7.10b/STAR_2.7.10b.zip && \
    unzip STAR_2.7.10b.zip && \
    rm STAR_2.7.10b.zip && \
    chmod a+x STAR_2.7.10b/Linux_x86_64_static/STAR && \
    mv STAR_2.7.10b/Linux_x86_64_static/STAR /bin/STAR && \
    rm -r STAR_2.7.10b
    
RUN wget https://github.com/COMBINE-lab/salmon/releases/download/v1.9.0/salmon-1.9.0_linux_x86_64.tar.gz && \
    tar -zxvf salmon-1.9.0_linux_x86_64.tar.gz && \
    rm salmon-1.9.0_linux_x86_64.tar.gz && \
    chmod a+x salmon-1.9.0_linux_x86_64/bin/salmon && \
    mv salmon-1.9.0_linux_x86_64/bin/salmon /bin/salmon && \
    rm -r salmon-1.9.0_linux_x86_64 

RUN wget https://github.com/arq5x/bedtools2/releases/download/v2.30.0/bedtools.static.binary -O /bin/bedtools.static.binary && \
    chmod a+x /bin/bedtools.static.binary && \
    echo 'alias bedtools="/bin/bedtools.static.binary"' >> /.bashrc

RUN wget https://github.com/samtools/samtools/archive/refs/tags/1.16.1.zip -O ./samtools-1.16.1.zip && \
    unzip samtools-1.16.1.zip && \
    rm samtools-1.16.1.zip && \
    mv samtools-1.16.1/misc samtools && \
    rm -r samtools-1.16.1 && \
    echo 'alias samtools="/samtools/samtools.pl"' >> /.bashrc
    
RUN wget https://github.com/broadinstitute/picard/releases/download/2.27.5/picard.jar -O /bin/picard.jar && \
    chmod a+x /bin/picard.jar && \
    echo 'alias picard="java -jar /bin/picard.jar"' >> /.bashrc


RUN pip install multiqc==1.13
```
