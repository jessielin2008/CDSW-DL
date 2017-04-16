##Preparing the GPUs

##Verify You Have a CUDA-Capable GPU
lspci | grep -i nvidia

##Enable the epel repo
mkdir deleteme && cd deleteme && \
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm && \
yum install -y epel-release-latest-7.noarch.rpm && \
cd .. && rm -rf deleteme

##Update the repo
yum -y update

##Get the latest kernel. Sometimes with older kernels the headers and devels do not match
yum install kernel 
echo "blacklist nouveau" >> /etc/modprobe.d/nvidia-installer-disable-nouveau.conf
echo "options nouveau modeset=0" >> /etc/modprobe.d/nvidia-installer-disable-nouveau.conf

*reboot

##Install the headers
yum -y install git pciutils dkms kernel-devel-$(uname -r) kernel-headers-$(uname -r) gcc gcc-c++
./NVIDIA-Linux-x86_64-367.57.run --kernel-source-path /lib/modules/$(uname -r)/source

## Search for your NVIDIA drivers
##MINE were http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/accelerated-computing-instances.html
## G2 Instances
#Product Type	GRID
#Product Series	GRID Series
#Product	GRID K520
#Operating System	Linux 64-bit
#Recommended/Beta	Recommended/Certified
wget http://us.download.nvidia.com/XFree86/Linux-x86_64/367.57/NVIDIA-Linux-x86_64-367.57.run
chmod 774 NVIDIA-Linux-x86_64-367.57.run
./NVIDIA-Linux-x86_64-367.57.run --kernel-source-path /lib/modules/${uname -r}/source

##Check the installation
cat /proc/driver/nvidia/version

##For CUDA Toolkit
##For CentOS/RHEL 7.2: 
wget http://developer.download.nvidia.com/compute/cuda/7.5/Prod/local_installers/cuda_7.5.18_linux.run 
rpm -ivh cuda-repo-rhel7-7-5-local-7.5-18.x86_64.rpm
yum -y install cuda
mkdir cuda-samples
/usr/local/cuda-7.5/bin/cuda-install-samples-7.5.sh cuda-samples
cd cuda-samples/NVIDIA_CUDA-7.5_Samples/
make
./bin/x86_64/linux/release/deviceQuery
./bin/x86_64/linux/release/bandwidthTest
