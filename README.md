
# Autoware Mini Docker

This repo provides a dockerized deployment of autoware mini with carla and is based on the code [here]() from X.

## Prerequisite
  1. Download  [Tartu.tar.gz]( https://drive.google.com/file/d/10CHEOjHyiLJgD13g6WwDZ2_AWoLasG2F/view?usp=share_link)  and place it inside `carlar0_9_13` folder 
  2. Download [Carla 0.9.13](https://carla-releases.s3.eu-west-3.amazonaws.com/Linux/CARLA_0.9.13.tar.gz) and extract PythonAPI folder and place it inside `autoware_mini/CARLA_ROOT/`
  3. Download [Autoware Mini]() and place it inside `autoware_mini/AUTOWARE_MINI/`
  4. Download [Scenario Runner]() and place it in `autoware_mini/SCENARIO_RUNNER/`. Upgrade shapely version from requirements.txt file in scenario runner to 2.0, because this is the minimum shapely version autoware mini supports.

## To build containers

    docker compose up --build

## To run carla sim 
    
    docker compose up carla_sim

## To run autoware mini
   
    docker compose up autoware_mini

If the docker-based carla deployment does not work, start CARLA on your host. Extract the CARLA 0.9.13 archive to ```/home/carla/``` and execute the following commands manually once inside the folder.

```bash
bash cp <path to Tartu.tar.gz> /home/carla/Import
bash chmod +x ./ImportAssets.sh
bash ./ImportAssets.sh
```

Start then the carla server:

```bash
bash ./CarlaUE4.sh -RenderOffScreen --world-port=2000
```

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

Consider available starting options explained in the autoware mini docs.

## FAQs

- Check that the DISPLAY variable is correctly set

## TODO

For development don´t copy repos, but mount them through volumes.

## Support

Lev Sorokin (lev.sorokin@tum.de)