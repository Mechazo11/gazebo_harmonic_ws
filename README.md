# Gazebo Harmonic workspace

A ROS 2 workspace containing all the packages necesasry to build and install ```Gazebo Harmonic``` from source. Based on the official tutorial : https://gazebosim.org/docs/harmonic/install_ubuntu_src/

* Install ```vcstool```, ```colcon``` and ```git```

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros2/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros2-latest.list'
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install python3-vcstool python3-colcon-common-extensions git
```

* Populate the src folder with ```Gazebo Harmonic``` packages

```bash
cd src/
curl -O https://raw.githubusercontent.com/gazebo-tooling/gazebodistro/master/collection-harmonic.yaml
vcs import < collection-harmonic.yaml
```

* Source a ROS 2 Humble/Jazzy workspace. In this example, I am using a ROS 2 Jazzy built from source

* Install all dependencies

```bash
sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
sudo apt-get update
sudo apt -y install \
  $(sort -u $(find . -iname 'packages-'`lsb_release -cs`'.apt' -o -iname 'packages.apt' | grep -v '/\.git/') | sed '/gz\|sdf/d' | tr '\n' ' ')
```

```bash
source ~/

```