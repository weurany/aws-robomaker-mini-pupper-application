# instructions for creating robomaker environment for mini pupper
## 1.create development environment
create development environment in aws robomaker console,</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#ides/new)</br>
remember to set "pre-installed robot software suite" to "Melodic"</br>
</br>
## 2. configure your development environment
* install colcon


```sh
sudo apt-get update
sudo apt-get install python3-pip python3-apt
pip3 install -U setuptools
pip3 install -U colcon-common-extensions colcon-ros-bundle
```

* configure the application


```sh
cd ~/environment
git clone --recursive https://github.com/0nhc/aws-robomaker-mini-pupper-application.git
cd robot_ws
colcon build
colcon bundle
cd ..
cd simulation_ws/src
git clone --recursive git@github.com:0nhc/minipupper_ros.git
cd ..
colcon build
colcon bundle
echo "source ~/environment/aws-robomaker-mini-pupper-application/aws-robomaker-mini-pupper-application/robot_ws/install/setup.bash" >> ~/.bashrc
echo "source ~/environment/aws-robomaker-mini-pupper-application/aws-robomaker-mini-pupper-application/simulation_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

* create bucket


create S3 buckets for mini pupper</br>
[check this link](https://s3.console.aws.amazon.com/s3/bucket/create?region=ap-northeast-1)</br>
set "bucket name" to "mini-pupper-bucket"</br>

* upload bundled file to bucket


return to aws development environment</br>
```sh
cd ~/environment/aws-robomaker-mini-pupper-application/robot_ws/bundle
aws s3api put-object --bucket mini-pupper-bucket --key mnpp_robot.tar --body output.tar
cd ~/environment/aws-robomaker-mini-pupper-application/simulation_ws/bundle
aws s3api put-object --bucket mini-pupper-bucket --key mnpp_sim.tar --body output.tar
```

## 3. create robot application
return to robomaker console</br>
then create a new robot application</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#robotApplications/new)</br>
set "name" to "mini-pupper-robot"</br>
set "robot software suite" to "Melodic"</br>
set "application" to "provide a colcon-bundled source file"</br>
and then click "browse S3"</br>
choose "mini-pupper-bucket/mnpp_robot.tar"</br>
then click "create"</br>

## 4. create simulation application
return to robomaker console</br>
then create a new simulation application</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#simulationApplications/new)</br>
set "name" to "mini-pupper-simulation"</br>
set "Simulation software suite" to "Melodic"</br>
set "application" to "provide a colcon-bundled source file"</br>
and then click "browse S3"</br>
choose "mini-pupper-bucket/mnpp_sim.tar"</br>
then click "create"</br>

## 5. create simulation job
return to robomaker console</br>
then create a new simulation job</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#simulationJobs/new)</br>

* step 1


in section "simulation job details"</br>
set "simulation job duration" to 1 hour(for saving money)</br>
set "robot software suite" to "Melodic"</br>
set "IAM role" to whatever you want</br>

in section "simulation job output"</br>
click "browse S3"</br>
choose "mini-pupper-bucket"</br>
just click "Next"</br>

* step 2


in section "choose method"</br>
click "select existing application"</br>

in section "robot application"</br>
choose "mini-pupper-robot"</br>

in section "robot application configuration"</br>
set "launch package name" to "hello_world_robot"</br>
set "launch file" to "rotate.launch"</br>
just click "Next"</br>

* step 3


in section "choose method"</br>
click "select existing application"</br>

in section "Simulation application"</br>
choose "mini-pupper-simulation"</br>

in section "Simulation application configuration"</br>
set "launch package name" to "mini_pupper"</br>
set "launch file" to "gazebo.launch"</br>
just click "Next"</br>

* step 4


just click "create"</br>
