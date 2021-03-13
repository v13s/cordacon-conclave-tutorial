# CordaCon 2020 Conclave tutorial

This is the code that was presented in the tutorial, which is available to watch on [YouTube](https://www.youtube.com/watch?v=WLBFDK2MEms).

##Update and Upgrade packages
apt update
apt upgrade -y

##Install Prerequisites

###Install OpenJDK 11
apt install openjdk-11-jdk

###Check if enclave is present
ls /dev/sgx

dmesg | grep sgx

###Add user to the sgx_prv group
sudo usermod -aG sgx_prv $USER

###check and confirm that the libraries are not present
ls /usr/lib/x86_64-linux-gnu/libdcap_quoteprov.so.1
ls /usr/lib/x86_64-linux-gnu/libdcap_quoteprov.so
ls /usr/lib/libdcap_quoteprov.so.1
ls /usr/lib/libdcap_quoteprov.so

###Update the Log level
export AZDCAP_DEBUG_LOG_LEVEL=FATAL

###Add 01.org to apt for SGX packages
echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" >> /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
apt-get update


###Install SGX runtime components
apt-get install -y -q libsgx-quote-ex libsgx-urts

###Install SGX DCAP SDK
apt-get install -y -q libsgx-dcap-ql libsgx-dcap-ql-dev

###install Azure plugin (this should work on Ubuntu 20 as well)
wget https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/az-dcap-client_1.6_amd64.deb
dpkg -i az-dcap-client_1.6_amd64.deb

###setup symlink
ln -s /usr/lib/libdcap_quoteprov.so /usr/lib/x86_64-linux-gnu/libdcap_quoteprov.so.1
ls /usr/lib/libdcap_quoteprov.so
ls  /usr/lib/x86_64-linux-gnu/libdcap_quoteprov.so.1

##Setup Conclave
cd /opt/
git clone https://github.com/R3Conclave/cordacon-conclave-tutorial.git

##Build Conclave Project
cd cordacon-conclave-tutorial/
./gradlew clean
./gradlew host:shadowJar
./gradlew client:shadowJar

##Execute 
###Use 'Screen' tool on Ubuntu

###Screen 1: Start HOST & Enclave
cd /opt/cordacon-conclave-tutorial/host/build/libs/
java -jar host-all.jar 8080

###Screen 2: Start Client A for Alice
cd /opt/cordacon-conclave-tutorial/client/build/libs/
java -jar client/build/libs/client-all.jar <<IPADDRESS>> 8080 Alice "C:3F74F2A5EA2C53265B5EA20B124A27889AC1E5BA8D00C31107558805B7FC065F SEC:INSECURE"

Enter Order: XYZ BUY 100 10

###Screen 3: Start Client B for Bob
cd /opt/cordacon-conclave-tutorial/client/build/libs/
java -jar client/build/libs/client-all.jar <<IPADDRESS>> 8080 Bob "C:3F74F2A5EA2C53265B5EA20B124A27889AC1E5BA8D00C31107558805B7FC065F SEC:INSECURE"

Enter Order: XYZ SELL 50 9.9

##Done
