# An OpenAI gym extension for using Gazebo known as `gym-gazebo`

<!--[![alt tag](https://travis-ci.org/erlerobot/gym.svg?branch=master)](https://travis-ci.org/erlerobot/gym)-->

This work presents an extension of the initial OpenAI gym for robotics using ROS and Gazebo. A whitepaper about this work is available at https://arxiv.org/abs/1608.05742. Please use the following BibTex entry to cite our work:

	@misc{1608.05742,
		Author = {Iker Zamora and Nestor Gonzalez Lopez and Victor Mayoral Vilches and Alejandro Hernandez Cordero},
		Title = {Extending the OpenAI Gym for robotics: a toolkit for reinforcement learning using ROS and Gazebo},
		Year = {2016},
		Eprint = {arXiv:1608.05742},
	}

Visit [erlerobotics/gym](http://erlerobotics.com/docs/Simulation/Gym/) for more information and videos.

## Table of Contents
- [Environments](#environments)
- [Installation](#installation)
	- [Docker](#docker)
	- [Ubuntu 16.04](#ubuntu-1604)
	- [Ubuntu 14.04 (**deprecated**)](#ubuntu-1404)
- [Usage](#usage)


## Environments
The following are some of the available gazebo environments:

| Name | Status | Description |
| ---- | ------ | ----------- |
| `GazeboCircuit2TurtlebotLidar-v0` | Maintained | A simple circuit with straight tracks and 90 degree turns. Highly discretized LIDAR readings are used to train the Turtlebot. Scripts implementing **Q-learning** and **Sarsa** can be found in the _examples_ folder. |
| `GazeboCircuit2TurtlebotLidarNn-v0` | | A simple circuit with straight tracks and 90 degree turns. A LIDAR is used to train the Turtlebot. Scripts implementing **DQN** can be found in the _examples_ folder.|
| `GazeboCircuit2cTurtlebotCameraNnEnv-v0` | | A simple circuit with straight tracks and 90 degree turns with high contrast colors between the floor and the walls. A camera is used to train the Turtlebot. Scripts implementing **DQN** using **CNN** can be found in the _examples_ folder. |

## Installation

### Docker

Build/fetch the container:
```bash
docker pull erlerobotics/gym-gazebo:latest # to fetch the container
# docker build -t gym-gazebo .
```

Enter the container
```bash
docker run -it erlerobotics/gym-gazebo
```

If you wish to run examples that require plugins like cameras, create a fake screen with:
```
xvfb-run -s "-screen 0 1400x900x24" bash
```

If you have an equivalent release of Gazebo installed locally, you can connect to the gzserver inside the container using gzclient GUI by setting the address of the master URI to the containers public address.
```
export GAZEBO_MASTER_IP=$(sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' "id of running container")
export GAZEBO_MASTER_URI=$GAZEBO_MASTER_IP:11345
gzclient
```

### Ubuntu 16.04
Basic requirements:
- ROS Kinetic (`/rosversion: 1.12.7`)
- Gazebo 8.1.1
- Python 3.5.2
- OpenCV3, installed from sources for Python 3 (`git clone https://github.com/Itseez/opencv.git`)

#### ROS Kinetic dependencies
```
sudo pip3 install rospkg catkin_pkg

sudo apt-get install python3-pyqt4

sudo apt-get install \
cmake gcc g++ qt4-qmake libqt4-dev \
libusb-dev libftdi-dev \
python3-defusedxml python3-vcstool \
ros-kinetic-octomap-msgs        \
ros-kinetic-joy                 \
ros-kinetic-geodesy             \
ros-kinetic-octomap-ros         \
ros-kinetic-control-toolbox     \
ros-kinetic-pluginlib	       \
ros-kinetic-trajectory-msgs     \
ros-kinetic-control-msgs	       \
ros-kinetic-std-srvs 	       \
ros-kinetic-nodelet	       \
ros-kinetic-urdf		       \
ros-kinetic-rviz		       \
ros-kinetic-kdl-conversions     \
ros-kinetic-eigen-conversions   \
ros-kinetic-tf2-sensor-msgs     \
ros-kinetic-pcl-ros \
ros-kinetic-navigation
```

```
#Install Sophus
cd
git clone https://github.com/stonier/sophus -b indigo
cd sophus
mkdir build
cd build
cmake ..
make
sudo make install
echo "## Sophus installed ##\n"
```

#### Gazebo gym

```bash
git clone https://github.com/erlerobot/gym-gazebo
cd gym-gazebo
sudo pip3 install -e .
```

If successful, expect something like [this](https://gist.github.com/vmayoral/4a1e5999811ac8bfbf7875670c430d27).

#### Dependencies and libraries
```
sudo pip3 install h5py
sudo apt-get install python3-skimage

# install Theano
cd ~/
git clone git://github.com/Theano/Theano.git
cd Theano/
sudo python3 setup.py develop

#install Keras
sudo pip3 install keras
```


#### Try it out (turtlebot simple env)

**Issues**:
- `spacenav_node` not compiling. `CATKIN_IGNORE`d.
- `wiimote` not compiling. `CATKIN_IGNORE`d.
- `kobuki_qtestsuite` not compiling. `CATKIN_IGNORE`d.


Agent dependencies:
```bash
cd gym_gazebo/envs/installation
bash setup_kinetic.bash		
```

Run the environment with a sample agent:
```bash
cd gym_gazebo/examples/scripts_turtlebot
python circuit2_turtlebot_lidar_qlearn.py
```

### Ubuntu 14.04
#### ROS Indigo

Install the Robot Operating System via:

**ros-indigo-desktop-full** is the only recommended installation.

- Ubuntu: http://wiki.ros.org/indigo/Installation/Ubuntu
- Others: http://wiki.ros.org/indigo/Installation

#### Gazebo

- Setup your computer to accept software from packages.osrfoundation.org:

```bash
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
```
- Setup keys:

```bash
wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
```
- Install Gazebo:

```bash
sudo apt-get update
sudo apt-get remove .*gazebo.* && sudo apt-get update && sudo apt-get install gazebo7 libgazebo7-dev
```

#### Gym Gazebo Pip

```bash
git clone https://github.com/erlerobot/gym-gazebo
cd gym-gazebo
sudo pip install -e .
```

#### Additional dependencies

There are two options to install dependencies: automatic installation or step-by-step installation

##### Automatic installation

Install dependencies running [setup_indigo.bash](gym_gazebo/envs/installation/setup_indigo.bash). If you are going to use DQN with Keras, also install [Keras and Theano](#keras-and-theano-installation).

```bash
cd gym_gazebo/envs/installation
bash setup_indigo.bash
```
Before running a environment, load the corresponding setup script. For example, to load the Turtlebot execute:

- Turtlebot

```bash
cd gym_gazebo/envs/installation
bash turtlebot_setup.bash
```

##### Step-by-step installation

Needs to be updated, use automatic installation.

<!--
**1.** Install dependencies

Install ubuntu packages
```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver hkp://pool.sks-keyservers.net --recv-key 0xB01FA116
sudo apt-get update
sudo apt-get install -y git                            \
                        mercurial                      \
                        libsdl-image1.2-dev            \
                        libspnav-dev                   \
                        libtbb-dev                     \
                        libtbb2                        \
                        libusb-dev libftdi-dev         \
                        pyqt4-dev-tools                \
                        python-vcstool                 \
                        ros-indigo-bfl                 \
                        python-pip                     \
                        g++                            \
                        ccache                         \
                        realpath                       \
                        libopencv-dev                  \
                        libtool                        \
                        automake                       \
                        autoconf                       \
                        libexpat1-dev                  \
                        ros-indigo-mavlink             \
                        ros-indigo-octomap-msgs        \
                        ros-indigo-joy                 \
                        ros-indigo-geodesy             \
                        ros-indigo-octomap-ros         \
                        ros-indigo-control-toolbox     \
                        gawk                           \
                        libtinyxml2-dev
```

Install python dependencies

```bash
sudo easy_install numpy
sudo pip2 install pymavlink MAVProxy catkin_pkg --upgrade
```

Install Sophus
```bash
cd gym_gazebo/envs/installation
git clone https://github.com/stonier/sophus -b indigo
cd sophus
mkdir build
cd build
cmake ..
make
sudo make install
```

Clone Ardupilot
```bash
cd ../..
mkdir apm && cd apm
git clone https://github.com/erlerobot/ardupilot -b gazebo_udp
```

Install JSBSim
```bash
git clone git://github.com/tridge/jsbsim.git
# Additional dependencies required
sudo apt-get install libtool automake autoconf libexpat1-dev
cd jsbsim
./autogen.sh --enable-libraries
make -j2
sudo make install
```

**2.** Create a catkin workspace

First load ROS environment variables
```bash
source /opt/ros/indigo/setup.bash
```
Then, create the catkin workspace inside `gym_gazebo/envs/installation/` directory

```bash
cd ../..
mkdir -p catkin_ws/src # Inside installation/ folder
cd catkin_ws/src
catkin_init_workspace
```
**3.** Import packages into catkin workspace and build

```bash
cd ../../catkin_ws/src/
vcs import < ../../gazebo.repos
cd ..
catkin_make --pkg mav_msgs
catkin_make
# If your computer crashes, try 'catkin_make -j1' instead
```
**4.** Add GAZEBO_MODEL_PATH to your `bashrc` and load it

```bash
cd ../../assets/models
echo "export GAZEBO_MODEL_PATH=\$GAZEBO_MODEL_PATH:$(pwd)" >> ~/.bashrc
source ~/.bashrc
```

**5.** Before running a environment, load the corresponding setup script. For example, to load the Turtlebot execute:

- Turtlebot

```bash
cd gym_gazebo/envs/installation
bash turtlebot_setup.bash
```
-->

##### Keras and Theano installation
This part of the installation is required only for the environments using DQN.

```bash
# install dependencies

sudo apt-get install gfortran

# install sript specific dependencies (temporal)
sudo apt-get install python-skimage

# install Theano
git clone git://github.com/Theano/Theano.git
cd Theano/
sudo python setup.py develop

#isntall Keras
sudo pip install keras
```
dot_parser error fix:
```bash
sudo pip install --upgrade pydot
sudo pip install --upgrade pyparsing
```

##### Enablig GPU for Theano

Follow the instructions [here](http://deeplearning.net/software/theano/install.html#gpu-linux) and change $PATH instead of $CUDA_ROOT.

Working on a clean installation of Ubuntu 14.04 using CUDA 7.5.

The following flags are needed in order to execute in GPU mode, using an alias is recommended.
```bash
THEANO_FLAGS=mode=FAST_RUN,device=gpu,floatX=float32
```

---

## Usage

### Build and install gym-gazebo

In the root directory of the repository:

```bash
sudo pip install -e .
```

### Running an environment

- Load the environment variables corresponding to the robot you want to launch. E.g. to load the Turtlebot:

```bash
cd gym_gazebo/envs/installation
bash turtlebot_setup.bash
```

Note: all the setup scripts are available in `gym_gazebo/envs/installation`

- Run any of the examples available in `examples/`. E.g.:

```bash
cd examples/scripts_turtlebot
python circuit2_turtlebot_lidar_qlearn.py
```

### Display the simulation

To see what's going on in Gazebo during a simulation, simply run gazebo client:

```bash
gzclient
```

### Display reward plot

Display a graph showing the current reward history by running the following script:

```bash
cd examples/utilities
python display_plot.py
```

HINT: use `--help` flag for more options.

### Killing background processes

Sometimes, after ending or killing the simulation `gzserver` and `rosmaster` stay on the background, make sure you end them before starting new tests.

We recommend creating an alias to kill those processes.

```bash
echo "alias killgazebogym='killall -9 rosout roslaunch rosmaster gzserver nodelet robot_state_publisher gzclient'" >> ~/.bashrc
```
