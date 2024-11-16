
# Autoware Mini Docker

This repo provides a dockerized deployment of [autoware mini](https://github.com/UT-ADL/autoware_mini) with carla and is based on the code [here](https://github.com/UT-ADL/autoware_mini/files/11582192/autoware_mini_docker_compose.zip).
 

## Prerequisite

Install [docker compose](https://docs.docker.com/compose/install/). Then download repositories which will be later mounted or copied into the containers.

  1. Download  [Tartu.tar.gz]( https://drive.google.com/file/d/10CHEOjHyiLJgD13g6WwDZ2_AWoLasG2F/view?usp=share_link)  and place it inside `carlar0_9_13` folder 
  2. Download [Carla 0.9.13](https://carla-releases.s3.eu-west-3.amazonaws.com/Linux/CARLA_0.9.13.tar.gz) and extract PythonAPI folder and place it inside `autoware_mini/CARLA_ROOT/`
  3. Download [Autoware Mini](https://github.com/UT-ADL/autoware_mini) and place it inside `autoware_mini/AUTOWARE_MINI/`
  4. Download Scenario Runner and place it in `autoware_mini/SCENARIO_RUNNER/` using:
  ``` bash
  git clone -b route_scenario https://github.com/UT-ADL/scenario_runner.git
  ```

   Upgrade shapely version from requirements.txt file in scenario runner to 2.0, because this is the minimum shapely version autoware mini supports.

## To build containers

    docker compose up --build

## To run carla sim 
    
    docker compose up carla_sim

If the docker-based carla deployment does not work, start CARLA on your host. Extract the CARLA 0.9.13 archive to ```/home/carla/``` and execute the following commands manually once inside the folder.

```bash
cp <path to Tartu.tar.gz> /home/carla/Import
chmod +x ./ImportAssets.sh
bash ./ImportAssets.sh
```
Start then the carla server in one terminal:

```bash
bash ./CarlaUE4.sh -RenderOffScreen --world-port=2000
```

## To run autoware mini

Open another terminal and run:
   
    docker compose up autoware_mini

## To customize starting script for autoware_mini

Update the command declaration for starting the container in the docker compose file. The default command launches autoware_mini integrated with carla.

```yaml
version: '3.1'
services:
  autoware_mini:
    stdin_open: true
    command:
      - "bash"
      - "-c"
      - >
        source /opt/catkin_ws/devel/setup.bash &&
        roslaunch autoware_mini start_carla.launch
```

You can update the command to activate the scenario_runner.

```yaml
version: '3.1'
services:
  autoware_mini:
    stdin_open: true
    command:
      - "bash"
      - "-c"
      - >
        source /opt/catkin_ws/devel/setup.bash &&
        roslaunch autoware_mini start_carla.launch use_scenario_runner:=true
```

Consider available starting options explained in the [autoware mini docs](https://github.com/UT-ADL/autoware_mini?tab=readme-ov-file#launching-with-scenario-runner).

## FAQs

- Check that the DISPLAY variable is correctly set.
- Check that permission are available to acces display. Give access by:
```bash
xhost +local:
```
- In case of problems accessing the GPU from the container, make sure that the nvidia container toolkit is installed and nvidia drivers are up-to-date.

## TODO

For development don´t copy repos, but mount them through volumes.

## Support

Lev Sorokin (lev.sorokin@tum.de)
