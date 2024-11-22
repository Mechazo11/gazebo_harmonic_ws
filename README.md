# Gazebo Harmonic workspace

A ROS 2 workspace containing all the packages necesasry to build and install ```Gazebo Harmonic``` from source along with all pertinent ```vendor``` packages for interfacing with ROS 2. Build instructions are based on the official tutorial : https://gazebosim.org/docs/harmonic/install_ubuntu_src/

## Setup Gazebo Harmonic

* Make sure you have ```vcstool```, ```colcon``` and ```git```.


* Download this repo in the `/home` directory and populate the `src` folder with ```Gazebo Harmonic``` packages

```bash
cd ~
git clone https://github.com/Mechazo11/gazebo_harmonic_ws.git
cd ~/gazebo_harmonic_ws
vcs import src < collection-harmonic.yaml
```


* Source a ROS 2 Humble/Jazzy workspace. In this example, I am using a ROS 2 Jazzy built from source in Ubuntu 22.04

* Install all dependencies

```bash
sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
sudo apt-get update
cd src/
sudo apt -y install \
  $(sort -u $(find . -iname 'packages-'`lsb_release -cs`'.apt' -o -iname 'packages.apt' | grep -v '/\.git/') | sed '/gz\|sdf/d' | tr '\n' ' ')
```

* Build the workspace

```bash
cd ..
source ~/ubuntu22_jazzy_ws/install/setup.bash
colcon build --cmake-args -DBUILD_TESTING=OFF -DCMAKE_CXX_FLAGS="-w" --merge-install
```

* Test if gazebo works correctly

```bash
source ./install/setup.bash
gz sim -v 4 empty.sdf
```

* If no output is seen or an error message stating ```looking for worlds```, then perform the following

```bash
source ./install/setup.bash
sudo ufw allow in proto udp to 224.0.0.0/4
sudo ufw allow in proto udp from 224.0.0.0/4
gz sim -v 4 empty.sdf
```