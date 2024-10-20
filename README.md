# Gazebo Harmonic workspace

A ROS 2 workspace containing all the packages necesasry to build and install ```Gazebo Harmonic``` from source. Based on the official tutorial : https://gazebosim.org/docs/harmonic/install_ubuntu_src/

## Setup Gazebo Harmonic

* Install ```vcstool```, ```colcon``` and ```git```

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros2/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros2-latest.list'
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install python3-vcstool python3-colcon-common-extensions git
```

* Populate the src folder with ```Gazebo Harmonic``` packages

```bash
vcs import src < collection-harmonic.yaml
```


* Source a ROS 2 Humble/Jazzy workspace. In this example, I am using a ROS 2 Jazzy built from source

* Install all dependencies

```bash
sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
sudo apt-get update
cd src/
sudo apt -y install \
  $(sort -u $(find . -iname 'packages-'`lsb_release -cs`'.apt' -o -iname 'packages.apt' | grep -v '/\.git/') | sed '/gz\|sdf/d' | tr '\n' ' ')
```

```bash
cd ..
source ~/ubuntu22_jazzy_ws/install/setup.bash
colcon build --cmake-args -DBUILD_TESTING=OFF --merge-install
```

* Delete ```build``` folder to save disk space: ```sudo rm -rf build/```

* Test to make sure Gazebo is loaded correctly

```bash
gz sim -v 4 empty.sdf
```

If you see a blank screen with the error ```[GUI] [Dbg] [Gui.cc:343] GUI requesting list of world names. The server may be busy downloading resources. Please be patient``` as discussed [here](https://gazebosim.org/docs/latest/troubleshooting/) follow the steps below

* If issue persists

```bash
sudo ufw allow in proto udp to 224.0.0.0/4
sudo ufw allow in proto udp from 224.0.0.0/4
gz sim -v 4 empty.sdf
```

## Setup gz vendors to connect with ROS 2

```bash
vcs import src < gz_jazzy.repos --recursive
rosdep install -r --from-paths src --rosdistro jazzy -i -y
source ~/ubuntu22_jazzy_ws/install/setup.bash
source ./install/setup.bash
colcon build --packages-select orocos_kdl --merge-install
source ./install/setup.bash
colcon build --cmake-args -DBUILD_TESTING=OFF --merge-install
```

