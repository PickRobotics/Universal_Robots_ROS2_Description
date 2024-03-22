# Universal_Robots_ROS2_Description

이 repository는 *Universal Robots* manipulators에 대한 description 파일과 meshes 파일을 포함하고 있습니다.

### Build status

ROS2 Distro | Branch | Build status | Released packages
:---------: | :----: | :----------: | :---------------:
**Humble** | [`humble`](https://github.com/UniversalRobots/Universal_Robots_ROS2_Description/tree/humble) | [![Build Status](https://build.ros2.org/job/Hbin_uJ64__ur_description__ubuntu_jammy_amd64__binary/badge/icon)](https://build.ros2.org/job/Hbin_uJ64__ur_description__ubuntu_jammy_amd64__binary/) | [ur_description](https://index.ros.org/p/ur_description/#humble)
**Iron** | [`iron`](https://github.com/UniversalRobots/Universal_Robots_ROS2_Description/tree/iron) | [![Build Status](https://build.ros2.org/job/Ibin_uJ64__ur_description__ubuntu_jammy_amd64__binary/badge/icon)](https://build.ros2.org/job/Ibin_uJ64__ur_description__ubuntu_jammy_amd64__binary/) | [ur_description](https://index.ros.org/p/ur_description/#iron)
**Rolling** | [`rolling`](https://github.com/UniversalRobots/Universal_Robots_ROS2_Description/tree/rolling) | [![Build Status](https://build.ros2.org/job/Rbin_uJ64__ur_description__ubuntu_jammy_amd64__binary/badge/icon)](https://build.ros2.org/job/Rbin_uJ64__ur_description__ubuntu_jammy_amd64__binary/)| [ur_description](https://index.ros.org/p/ur_description/#rolling)

A more [detailed build status](ci_status.md) shows the state of all CI workflows inside this repo.
Please note that the detailed view is intended for developers, while the one here should give end
users an overview of the current released state.


Note that for ROS2 **Foxy** the description is in the [driver's
repository](https://github.com/UniversalRobots/Universal_Robots_ROS2_Driver/tree/foxy). Please do
not clone this repository into a Foxy workspace.

## License
The [UR20 meshes](ur_description/meshes/ur20) and [UR30 meshes](ur_description/meshes/ur30) constitutes “Graphical Documentation” the use of which is subject to and governed by our “[Terms and Conditions for use of Graphical Documentation](https://www.universal-robots.com/legal/terms-and-conditions/terms_and_conditions_for_use_of_graphical_documentation.txt)”.

Universal Robots' [Terms and Conditions for use of Graphical Documentation](https://www.universal-robots.com/legal/terms-and-conditions/terms_and_conditions_for_use_of_graphical_documentation.txt) do not fully comply with [OSI's definition of Open Source](https://opensource.org/osd/), but they do allow you to use, modify and share “Graphical Documentation”, including [UR20](meshes/ur20) and [UR30](meshes/ur30) meshes, subject to certain restrictions.\
If you have any questions regarding this license or if this license doesn't fit your use-case, please contact [legal@universal-robots.com](mailto:legal@universal-robots.com).

All other content is licensed under the BSD-3-Clause license

## Structure of the repository

가장 관련있는 파일들 :
  - `urdf/ur_macro.xacro` - UR-manipulator description이 포함된 매크로 파일입니다. 이 파일은 일반적으로 외부 프로젝트에 포함되어 UR manipulators를 정확하게 시각화 및 구성하기 위해 사용됩니다. 이 매크로를 사용하는 방법의 예는 `urdf/ur.urdf.xacro` 파일에 있습니다.
  - `urdf/ur.ros2_control.xacro` - `ros2_control` 프레임워크를 위해서 manipulator의 joint와 인터페이스를 정의.

## manipulator의 description을 테스팅

robot 시각화를 위해서 이 repository를 여러분의 workspace에 설치하고 아래와 같이 실행하세요.:
```
ros2 launch ur_description view_ur.launch.py ur_type:=ur5e
```

`ur_type` 인자를 변경하여 다른 descriptions을 테스트할 수 있습니다.

## Package / Description structure
이 패키지는 모든 로봇에 대해 하나의 description을 사용합니다.  다양한 로봇 변형은 네 개의 설정 파일을 사용하여 구성됩니다. 이러한 파일들은 description을 추가로 커스터마이징하기 위해 변경할 수도 있습니다.

![urdf structure](doc/structure.svg)

기본적으로 description은 다음과 같은 4개의 파일에 저장된 설정값을 사용하여 수정할 수 있습니다.:
 - `config/urXX/default_kinematics.yaml` - This contains the calibration values as they can be
   extracted from the robot. Changing these values with the one extracted from a real robot will
   result in a description matching the real robot exactly (w.r.t the `tool0` frame). It is highly
   recommended to use matching kinematic values in real-world applications.
 - `config/urXX/joint_limits.yaml` - 로봇의 조인트 제한을 더 제한하려면, 이러한 제한을 여기에서 수정할 수 있습니다.
 - `config/urXX/physical_parameters.yaml` - 물리 시뮬레이션 파라미터(예: 관성 포즈 및 값)에 대한 모든 것은 여기에서 조정할 수 있습니다.
 - `config/urXX/visual_parameters.yaml` - 일부 사용자는 wrist_3_link의 cap을 교체하는 것과 같이 특정 시각적 측면을 변경합니다. 이 설정 파일은 사용해야 하는 메쉬(시각적, 충돌 모두)를 지정합니다.

4개의 설정 파일은 `ur.urdf.macro` 내부에서 수행되는 `ur_macro.urdf` (해당 파일에서 정의된 매크로에 더 구체적인)에 전달되어야 합니다. 파일 내용은 `ur_common.xacro` 내부에서 파싱됩니다.

Arguments that have to be passed to the main `ur.urdf.xacro` file are:
 - kinematics_params - Filename to the `default_kinematics.yaml` (or equivalent specific kinematics) file
 - joint_limit_params - Filename to the `joint_limits.yaml` file
 - physical_params - Filename to the `physical_parameters.yaml` file
 - visual_params - Filename to the `visual_params.yaml` file

The launchfile `launch/view_ur.launch.py` abstracts these four parameters to one `ur_type` argument
which will basically replace the `urXX` part of the paths as shown in the picture above.

## Creating your own description including this description
In real-world applications you will most probably have a more complex description consisting of more
objects than just the robot. It is recommended to create a separate ROS package containing this
particular description. Inside this description you could also store your robot-specific kinematics
parameters file.

As mentioned above, see the `urdf/ur.urdf.xacro` file as an example to integrate a UR robot into
your scene description. Basically, you could create a copy of that file and extend it with the
modifications from your specific scene.
