# pace-commoncrawl-scanner
Scans CommonCrawl datasets for keywords. Scans the whole month of CommonCrawl data using Amazon EC2 c5n.16xlarge instance for hundreds of keywords in about 4 hours. Developed with support from the EU and the Populism &amp; Civic Engagement H2020 project.

![screenshot](https://yrpri-eu-direct-assets.s3-eu-west-1.amazonaws.com/Screenshot+from+2021-01-27+23-40-42.png)

## Various setup steps for installing on a AWS Ubuntu 20.04
```bash
wget -O- https://apt.corretto.aws/corretto.key | sudo apt-key add - 
sudo add-apt-repository 'deb https://apt.corretto.aws stable main'
sudo apt-get update; sudo apt-get install -y java-15-amazon-corretto-jdk

sudo apt install build-essential cmake libboost-all-dev ragel maven

git clone git://github.com/intel/hyperscan
cd hyperscan
cmake -DBUILD_SHARED_LIBS=YES
make 
sudo make install

cd

git clone https://github.com/CitizensFoundation/pace-commoncrawl-scanner.git
cd pace-commoncrawl-scanner
mvn clean package

mkdir /home/ubuntu/pace-commoncrawl-scanner/results

cd /home
sudo ln -s ubuntu/ robert

cd
cd pace-commoncrawl-scanner
```

## Prepare the page ranks file into the condensed format
```bash
processScripts/getLatestPageRanking.sh 2020 11 https://commoncrawl.s3.amazonaws.com/projects/hyperlinkgraph/cc-main-2020-jul-aug-sep/host/cc-main-2020-jul-aug-sep-host-ranks.txt.gz
processScripts/processHostRanksFile.sh 2020 11
```

## Step 1 - Download files list
```bash
processScripts/getLatestWetPathsAndDownloadAll.sh 2020 11 https://commoncrawl.s3.amazonaws.com/crawl-data/CC-MAIN-2020-50/wet.paths.gz 72000
```

## Step 2- Download, gunzip and scan the files
```bash
processScripts/scan.sh 2020 11
```

## Step 3 - Import into ElasticSearch (can be done in parallel with step 2)
```bash
processScripts/importToES.sh 2020 11
```

![eu logo](https://demos-h2020.eu/img/EU_logo.jpg)

This project has received funding from the European Union’s Horizon 2020 research and innovation programme under grant agreement No 822337. Any dissemination of results here presented reflects only the consortium’s view. The Agency is not responsible for any use that may be made of the information it contains.
