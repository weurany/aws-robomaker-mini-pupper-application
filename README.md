# instructions for creating robomaker environment for mini pupper
## 1.create development environment
create development environment in aws robomaker console,
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#ides/new)
remember to set <pre-installed robot software suite> to <Melodic>

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
git colne https://github.com/0nhc/aws-robomaker-mini-pupper-application.git
cd robot_ws
colcon build
colcon bundle
cd ..
cd simulation_ws
colcon build
colcon bundle
echo "source ~/environment/aws-robomaker-mini-pupper-application/robot_ws/install/setup.bash" >> ~/.bashrc
echo "source ~/environment/aws-robomaker-mini-pupper-application/simulation_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

* create bucket
create S3 buckets for mini pupper
[check this link](https://s3.console.aws.amazon.com/s3/bucket/create?region=ap-northeast-1)
set <bucket name> to <mini-pupper-bucket>

* upload bundled file to bucket
return to aws development environment
```sh
cd ~/environment/aws-robomaker-mini-pupper-application/robot_ws/bundle
aws s3api put-object --bucket mini-pupper-bucket --key mnpp_robot.tar --body output.tar
cd ~/environment/aws-robomaker-mini-pupper-application/simulation_ws/bundle
aws s3api put-object --bucket mini-pupper-bucket --key mnpp_sim.tar --body output.tar
```

## 3. create robot application
return to robomaker console
then create a new robot application
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#robotApplications/new)
set <name> to <mini-pupper-robot>
set <robot software suite> to <Melodic>
set <application> to <provide a colcon-bundled source file>
and then click <browse S3>
choose <mini-pupper-bucket/mnpp_robot.tar>
then click <create>

## 3. create simulation application
return to robomaker console
then create a new simulation application
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#robotApplications/new)
set <name> to <mini-pupper-simulation>
set <robot software suite> to <Melodic>
set <application> to <provide a colcon-bundled source file>
and then click <browse S3>
choose <mini-pupper-bucket/mnpp_sim.tar>
then click <create>

## 3. create simulation job
return to robomaker console
then create a new simulation job
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#simulationJobs/new)
* step 1
in section <Simulation job details>
set <simulation job duration> to 1 hour(for saving money)
set <robot software suite> to <Melodic>
set <IAM role> to whatever you want

in section <simulation job output>
click <browse S3>
choose <mini-pupper-bucket>

* step 2
in section <choose method>
click <select existing application>

in section <robot application>
choose <mini-pupper-robot>

in section <Robot application configuration>
set <launch package name> to <hello_world_robot>
set <launch file> to <rotate.launch>

* step 3
in section <choose method>
click <select existing application>

in section <robot application>
choose <mini-pupper-simulation>

in section <Robot application configuration>
set <launch package name> to <mini_pupper>
set <launch file> to <gazebo.launch>

* step 4
just click <create>
