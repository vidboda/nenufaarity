BootStrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum

%labels

	AUTHOR david.baux@inserm.fr

%post

	echo "Updating CentOS and installing mandatory packages"
	yum -y update
	yum -y group install "Development Tools" 
	yum -y install wget git python-devel zlib-devel bzip2-devel xz-devel libxml2 libxml2-devel libxslt libxslt-devel ncurses-devel which
	#java-1.8.0-openjdk

	echo "Installing FastQC..."
	wget http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip
	unzip fastqc_v0.11.5.zip
 	chmod 755 FastQC/fastqc
	ln -s /FastQC/fastqc /usr/local/bin/fastqc
	rm fastqc_v0.11.5.zip

	echo "Installing BWA..."
	git clone https://github.com/lh3/bwa.git
	cd bwa
	make
	mv bwa /usr/local/bin/
	cd ..
	rm -rf bwa/

	echo "Installing HTSlib & Samtools"
	wget https://github.com/samtools/htslib/releases/download/1.6/htslib-1.6.tar.bz2
	tar -xjf htslib-1.6.tar.bz2
	rm htslib-1.6.tar.bz2
	cd htslib-1.6
	./configure --prefix=/usr/local/
	make 
	make install
	cd ..
	rm -rf htslib-1.6	

	wget https://github.com/samtools/samtools/releases/download/1.6/samtools-1.6.tar.bz2
	tar -xjf samtools-1.6.tar.bz2
	rm samtools-1.6.tar.bz2
	cd samtools-1.6
	./configure --prefix=/usr/local/
	make
	make install
	cd ..
	rm -rf samtools-1.6

	wget https://github.com/samtools/bcftools/releases/download/1.6/bcftools-1.6.tar.bz2
	tar -xjf bcftools-1.6.tar.bz2
	rm bcftools-1.6.tar.bz2
	cd bcftools-1.6
	./configure --prefix=/usr/local/
        make
	make install
        cd ..
        rm -rf bcftools-1.6

	echo "Installing sambamba..."
	wget https://github.com/lomereiter/sambamba/releases/download/v0.6.6/sambamba_v0.6.6_linux.tar.bz2
	tar -xjf sambamba_v0.6.6_linux.tar.bz2
	rm sambamba_v0.6.6_linux.tar.bz2	
	mv sambamba_v0.6.6 /usr/local/bin

	echo "Installing bedtools..."
	wget https://github.com/arq5x/bedtools2/releases/download/v2.26.0/bedtools-2.26.0.tar.gz
	tar -xzf bedtools-2.26.0.tar.gz
	rm bedtools-2.26.0.tar.gz
	cd bedtools2
	make
	mv ./bin/* /usr/local/bin/
	cd ..
	rm -rf bedtools2

	echo "Installing Oracle's java..."
	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.rpm"
	yum localinstall -y jdk-8u151-linux-x64.rpm
	rm jdk-8u151-linux-x64.rpm

	echo "Installing libxslt for jvarkit"
	wget https://git.gnome.org/browse/libxslt/snapshot/libxslt-1.1.31.zip
	unzip libxslt-1.1.31.zip
	rm libxslt-1.1.31.zip
	cd libxslt-1.1.31
	./autogen.sh --prefix=/usr/local
	make
	make install
	cd ..
	rm -rf libxslt-1.1.31

	echo "Installing nenufaar..."
	git clone https://github.com/beboche/nenufaar
	cd nenufaar
	mkdir software/
	mkdir software/GenomeAnalysisTK/
	mkdir software/GenomeAnalysisTK/3.8.0/
	mkdir software/picard/
	mkdir software/picard/2.13.2/
	mkdir software/Queue/
        mkdir software/Queue/3.8.0/
	echo "Downloading Picard..."
        wget https://github.com/broadinstitute/picard/releases/download/2.13.2/picard.jar
	mv picard.jar software/picard/2.13.2/
	echo "Downloading Queue..."
	wget -O Queue-3.8-0.tar.bz2 "https://software.broadinstitute.org/gatk/download/auth?package=Queue"
        tar -xjf Queue-3.8-0.tar.bz2 
	mv ./Queue-3.8-0-ge9d806836/Queue.jar software/Queue/3.8.0/
        rm Queue-3.8-0.tar.bz2
	rm -rf ./Queue-3.8-0-ge9d806836/
	echo "Downloading GATK.."
	wget -O GenomeAnalysisTK-3.8-0.tar.bz2 "https://software.broadinstitute.org/gatk/download/auth?package=GATK"
	tar -xjf GenomeAnalysisTK-3.8-0.tar.bz2
	mv ./GenomeAnalysisTK-3.8-0-ge9d806836/GenomeAnalysisTK.jar software/GenomeAnalysisTK/3.8.0/
	rm GenomeAnalysisTK-3.8-0.tar.bz2
	rm -rf ./GenomeAnalysisTK-3.8-0-ge9d806836/	

	echo "Installing jvarkit vcfpolyx..."
	cd software
	git clone https://github.com/lindenb/jvarkit.git
        cd jvarkit
        make vcfpolyx
	
	echo "Installing Platypus..."
	cd ..
	rpm -iUvh https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
        yum -y install python-pip
        pip install cython
	git clone https://github.com/andyrimmer/Platypus
        cd Platypus/
        make
	
#	echo "Installing Cava..."
#       cd ..
#	git clone https://github.com/RahmanTeam/CAVA
#        cd CAVA
#        ./install.sh


%runscript

	echo "try running with -app nenufaar/mnenufaar or singularity help nenufaarity.simg to get help"

##############################
# Nenufaar
##############################

%apprun nenufaar

	echo "Launching Nenufaar in Singularity container"
	exec /bin/sh /nenufaar/nenufaar.sh "$@"

%apphelp nenufaar

	Nenufaar is the core pipeline:
	create a run folder inside input/, and then place inside one folder per samples and the 2 fastqs inside,
	e.g. input/run/sample1/sample1.R1.fastq.gz and input/run/sample1/sample1.R2.fastq.gz
	nenufaar will treat all samples one after another.
	-run nenufaar with the command:
	singularity run --app nenufaar nenufaarity.simg -i input/ -g hg19 &
	-get specific help with optons description
	singularity run --app nenufaar nenufaarity.simg -h


##############################
# Meta Nenufaar
##############################
	
%apprun mnenufaar

	echo "Launching MetaNenufaar in Singularity container"
	exec /bin/sh /nenufaar/mnenufaar.sh "$@"
	
%apphelp mnenufaar
	
	Meta Nenufaar is a script that can run nenufaar on a family
	the variant calling is performed on a merged BAM and the output is a single vcf for all samples
	It might be barcoded with the nenufaar_annot.sh script which is not included in this container (see https://github.com/beboche/nenufaar).
	Create a family folder inside input/, and then place inside one folder per samples and the 2 fastqs inside,
	-run mnenufaar with the command:
	singularity run --app mnenufaar nenufaarity.simg -i input/family/ -g hg19 &
	-get specific help with options description
	singularity run --app mnenufaar nenufaarity.simg -h

	
%help
	
	this container can run either Nenufaar or Meta Nenufaar (mnenufaar, e.g. for families)
	-to get specific help on Nenufaar:
	singularity help --app nenufaar nenufaarity.simg
	-on Meta Menufaar:
	singularity help --app mnenufaar nenufaarity.simg

