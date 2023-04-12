Codigos previos del equipo
==========================

Introducción
------------

Durante la existencia del equipo Keybot 5716, desde su fundación en
2015, se han usado diferentes codigos adaptados para el robot de la
temporada y los mecanismos desarrollados para este mismo. Cabe destacar
que a partir del *Guadalajara Off-Season (2022)* el codigo de robot paso
de **Python a C++**, por lo que tanto el robot **Solaris** como el robot
**Marte** fueron programados en C++.

Codigo Base
-----------

Los robots funcionan gracias al framework conocido como **Timed Skeleton
(Advanced)** que provee WPI en la libreria WPILib, este se consigue
gracias al Creador de proyectos incluido en la extensión de de WPILib y
la “Command Palette” incluida en este. En la imagen de abajo podemos ver
un ejemplo este menu.

.. figure:: img/project_creator.png
    :alt: Project Creator

Esta configuración genera dos archivos **Robot.cpp** y **Robot.h**,
antes de mostrar el contenido de la plantilla base es importante saber
que significa cada una de las extensiones de los archivos.

-  **“.cpp”**: Es un archivo que contienen código fuente escrito en el
   lenguaje de programación orientado a objetos C++.
-  **“.h”**: Los archivos *.h*, presentes en los lenguajes de
   programación C y C++,son llamados como los ‘archivos de cabecera’ por
   los programadores. Pueden consistir en definiciones de *variables
   externas*, *prototipos de funciones* y *constantes*.

Ya aclarado esto, el codigo por defecto para la plantilla **Timed
Skeleton (Advanced)** es el siguiente:

.. tabs::

	.. tab:: **Robot.cpp**

		.. code-block:: c++

			// Copyright (c) FIRST and other WPILib contributors.
			// Open Source Software; you can modify and/or share it under the terms of
			// the WPILib BSD license file in the root directory of this project.

			#include "Robot.h"

			void Robot::RobotInit() {}
			void Robot::RobotPeriodic() {}

			void Robot::AutonomousInit() {}
			void Robot::AutonomousPeriodic() {}

			void Robot::TeleopInit() {}
			void Robot::TeleopPeriodic() {}

			void Robot::DisabledInit() {}
			void Robot::DisabledPeriodic() {}

			void Robot::TestInit() {}
			void Robot::TestPeriodic() {}

			void Robot::SimulationInit() {}
			void Robot::SimulationPeriodic() {}

			#ifndef RUNNING_FRC_TESTS
			int main() {
			    return frc::StartRobot<Robot>();
			}
			#endif
			
	.. tab:: **Robot.h**

		.. code-block:: c++

			// Copyright (c) FIRST and other WPILib contributors.
			// Open Source Software; you can modify and/or share it under the terms of
			// the WPILib BSD license file in the root directory of this project.

			#pragma once

			#include <frc/TimedRobot.h>

			class Robot : public frc::TimedRobot {
			    public:
			        void RobotInit() override;
			        void RobotPeriodic() override;

			        void AutonomousInit() override;
			        void AutonomousPeriodic() override;

			        void TeleopInit() override;
			        void TeleopPeriodic() override;

			        void DisabledInit() override;
			        void DisabledPeriodic() override;

			        void TestInit() override;
			        void TestPeriodic() override;

			        void SimulationInit() override;
			        void SimulationPeriodic() override;
			};

Solaris - Rapid React
---------------------

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque sed ipsum erat. Integer neque velit, rutrum a ex ac, iaculis pulvinar lectus. Nullam auctor auctor facilisis. Phasellus leo odio, euismod id metus eu, blandit tempor lorem. Morbi id nunc ultricies nibh luctus hendrerit sit amet quis metus. Cras faucibus erat id pulvinar vulputate. Quisque sit amet auctor mauris. Sed eu orci nisi. Curabitur bibendum tristique urna quis placerat.

.. tabs::

	.. tab:: **Robot.cpp (Teleop Only)** 

		.. code-block:: c++
		
			#include "Robot.h"

			int contVar = 0;
			auto ValidTARGET = false; int countACT = 0; int powerONE = 0.67; int powerTWO = -0.7;
			auto inst = nt::NetworkTableInstance::GetDefault(); auto pc = inst.GetTable("SmartDashboard");
			std::shared_ptr<nt::NetworkTable> limelight = nt::NetworkTableInstance::GetDefault().GetTable("limelight");

			Encoder encoderOne{0, 1, false, Encoder::EncodingType::k2X};
			Encoder encoderTwo{2, 3, false, Encoder::EncodingType::k2X};

			void Robot::RobotInit() {
			  CameraServer::StartAutomaticCapture();
			  cs::CvSink cvSink = CameraServer::GetVideo();
			  cs::CvSource outputStream = CameraServer::PutVideo("Blur", 640, 480);
			}

			void Robot::RobotPeriodic() {}
			void Robot::AutonomousInit() {}
			void Robot::AutonomousPeriodic() {}

			void Robot::TeleopInit() {}

			void Robot::TeleopPeriodic() {

			  double tx = table->GetNumber("tx",0.0);                         //targetOffsetAngle_Horizontal
			  double ty = table->GetNumber("ty",0.0);                         //targetOffsetAngle_Vertical
			  double ta = table->GetNumber("ta",0.0);                         //targetArea
			  double ts = table->GetNumber("ts",0.0);                         //targetSkew
			  double tv = table->GetNumber("tv",0.0);                         //getIfValidTargets

			  int switchCamCom = false;
			  float POW = 0.0;

			  Robot::invertMotors();

			  int povANGLE = DriverStation::GetStickPOV(1,0);

			  SmartDashboard::PutNumber("GetStickPOV", povANGLE);

			  double x_AXIS = m_stickONE.GetRawAxis(4);
			  double y_AXIS = m_stickONE.GetRawAxis(1);

			  bool leftTrigger =  m_stickONE.GetRawAxis(2);   //Trigger -> Disparo Upper-Hub
			  bool rightTrigger = m_stickONE.GetRawAxis(3);   //Trigger -> Disparo Lower-Hub

			  double botonA = m_stickONE.GetRawButton(1);     //Boton -> Escupir pelota
			  double botonB = m_stickONE.GetRawButton(2);     //Boton -> Tragar pelota
			  double botonY = m_stickONE.GetRawButton(4);     //Boton -> Compresor

			  double x = x_AXIS;
			  double y = y_AXIS;

			  double powerX = x<0.2 && x>-0.2 ? 0 : x;
			  double powerY = y<0.2 && y>-0.2 ? 0 : y;

			  if (rightTrigger){
			    SmartDashboard::PutString("Shooting", "Shooter: ON");
			    falconONE.Set(ControlMode::PercentOutput,0.55);
			    falconTWO.Set(ControlMode::PercentOutput,-0.55);
			  }else if (leftTrigger){
			    SmartDashboard::PutString("Shooting", "Shooter: ON");
			    falconONE.Set(ControlMode::PercentOutput,0.85);
			    falconTWO.Set(ControlMode::PercentOutput,-0.85);
			  }else{
			    SmartDashboard::PutString("Shooting", "Shooter: OFF");
			    falconONE.Set(ControlMode::PercentOutput,0);
			    falconTWO.Set(ControlMode::PercentOutput,0);
			  }

			  if (botonB){
			    SmartDashboard::PutNumber("Sucking", 1);
			    o_sucker.Set(ControlMode::PercentOutput,1);
			  }else if (botonA){
			    SmartDashboard::PutNumber("Sucking", -1);
			    o_sucker.Set(ControlMode::PercentOutput,-1);
			  } else{
			    SmartDashboard::PutNumber("Sucking", 0);
			    o_sucker.Set(ControlMode::PercentOutput,0);
			  }

			  if (switchCamCom){
			    if (botonY){
			      compressorMAIN.Start();
			    }else{
			      compressorMAIN.Stop();
			    }
			  } else{
			    if (tv == 0){
			      SmartDashboard::PutBoolean("Limelight has target: ", false);
			    } else{
			      SmartDashboard::PutBoolean("Limelight has target: ", true);
			      if (botonY){powerX = tx * 0.05;}
			    }
			  }
			  /*
			  if (povANGLE == 0){
			    solenoidZERO.Set(false);
			    solenoidTWO.Set(false);
			    solenoidONE.Set(true);
			  }else if(povANGLE == 90){
			    solenoidZERO.Set(true);
			    solenoidTWO.Set(false);
			    solenoidONE.Set(false);
			  }else{
			    solenoidTWO.Set(true);
			    solenoidONE.Set(false);
			    solenoidZERO.Set(false);
			  }*/

			  if (botonLB){POW = -0.6;}
			  else{POW = -0.9;}

			  SmartDashboard::PutNumber("Power Y", powerX*POW);

			  if (powerY < 0){
			    m_drive.ArcadeDrive(powerY*-POW,(powerX*-POW)-0.3);
			  }else if (powerY > 0){
			    m_drive.ArcadeDrive(powerY*-POW,(powerX*-POW)+0.3);
			  } else{
			    m_drive.ArcadeDrive(powerY*-POW,powerX*-POW);
			  }
			}

			void Robot::DisabledInit() {}
			void Robot::DisabledPeriodic() {}
			void Robot::TestInit() {}
			void Robot::TestPeriodic() {}
			void Robot::SimulationInit() {}
			void Robot::SimulationPeriodic() {}
			
			#ifndef RUNNING_FRC_TESTS
			int main() {
			  return StartRobot<Robot>();
			}
			#endif

	.. tab:: **Robot.h (Teleop Only)** 

		.. code-block:: c++
			
			#pragma once

			#include <string>
			#include <opencv2/core/core.hpp>
			#include <opencv2/highgui/highgui.hpp>
			#include <opencv2/imgproc/imgproc.hpp>
			#include <chrono>
			#include <iostream>
			#include <frc/TimedRobot.h>
			#include <frc/smartdashboard/SendableChooser.h>
			#include <fmt/core.h>
			#include <frc/smartdashboard/SmartDashboard.h>
			#include <frc/Joystick.h> // Joystick Library
			#include <frc/motorcontrol/Talon.h> //Talon Library
			#include <frc/motorcontrol/MotorControllerGroup.h> //Motor Library
			#include <frc/drive/DifferentialDrive.h> // Drive Library
			#include <frc/Servo.h>
			#include <frc/Compressor.h>
			#include <frc/Solenoid.h>
			#include <frc/DoubleSolenoid.h>
			#include <frc/Timer.h>
			#include <frc/Encoder.h>
			#include <frc/DriverStation.h>
			#include "frc/smartdashboard/Smartdashboard.h"
			#include "ctre/Phoenix.h" //Falcon Library
			#include "networktables/NetworkTable.h" //Network Tables Library
			#include "networktables/NetworkTableEntry.h" //Network Tables Entry Library
			#include "networktables/NetworkTableValue.h" //Network Tables Value Library
			#include "cameraserver/CameraServer.h" //Camera Server Library
			#include "networktables/NetworkTableInstance.h" //Network Tables Instance Library
			#include "wpi/span.h"
			#include "cscore_oo.h"

			using namespace std;
			using namespace cv;
			using namespace frc;

			class Robot : public frc::TimedRobot {
			  public:
			    void RobotInit() override;
			    void RobotPeriodic() override;
			    void AutonomousInit() override;
			    void AutonomousPeriodic() override;
			    void TeleopInit() override;
			    void TeleopPeriodic() override;
			    void DisabledInit() override;
			    void DisabledPeriodic() override;
			    void TestInit() override;
			    void TestPeriodic() override;
			    void SimulationInit() override;
			    void SimulationPeriodic() override;

			    double delta = 10;
			    double vi = 30;
			    double vf = 70;

			    double contAuto = 0;

			    void invertMotors(){
			      motorTWO.SetInverted(true); 
			      motorONE.SetInverted(true); 
			    }

			  private:
			    std::shared_ptr<nt::NetworkTable> table = nt::NetworkTableInstance::GetDefault().GetTable("limelight");
			    frc::SendableChooser<std::string> m_chooser;

			    const std::string kAutoNameDefault = "Default";
			    const std::string kAutoNameCustom = "My Auto";
			    std::string m_autoSelected;

			    frc::Joystick m_stickONE{1};  //Joystick Movimiento
			    //frc::Joystick m_stickTWO{2};  //Joystick Botones

			    frc::Compressor compressorMAIN{0, frc::PneumaticsModuleType::CTREPCM};

			    frc::Solenoid solenoidZERO{frc::PneumaticsModuleType::CTREPCM, 0};
			    frc::Solenoid solenoidONE{frc::PneumaticsModuleType::CTREPCM, 1};
			    frc::Solenoid solenoidTWO{frc::PneumaticsModuleType::CTREPCM, 2};

			    WPI_TalonSRX motorFOUR = {1};      //Talon 4 -> ID 01 Talon
			    WPI_TalonSRX motorTHREE = {0};       //Talon 3 -> ID 00 Talon
			    WPI_VictorSPX motorTWO = {0};      //Talon 2 -> ID 00 Victor
			    WPI_VictorSPX motorONE = {1};     //Talon 1 -> ID 01 Victor

			    TalonSRX o_sucker = {2};

			    TalonSRX falconONE = {4}; //Falcon 2 -> Identificador 1
			    TalonSRX falconTWO = {3}; //Falcon 3 -> Identificador 2

			    double botonLB = m_stickONE.GetRawButton(5);  //Boton -> Disminuir velocidad

			    frc::MotorControllerGroup m_leftGroup{motorTHREE, motorFOUR};     //Grupo de motores -> Izquierdo
			    frc::MotorControllerGroup m_rightGroup{motorTWO, motorONE};    //Grupo de motores -> Derecho

			    frc::DifferentialDrive m_drive{m_leftGroup, m_rightGroup};  //Conduccion de tipo Arcade

			    double countsPR = 256;
			    double wheelDiam = 2.3;

			};

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque sed ipsum erat. Integer neque velit, rutrum a ex ac, iaculis pulvinar lectus. Nullam auctor auctor facilisis. 

.. tabs::

	.. tab:: **Robot.cpp (Auto Only)** 

		.. code-block:: c++

			#include "Robot.h"

			void Robot::RobotInit() {
			  chooserAuto.SetDefaultOption(autoDefault, autoDefault);
			  chooserAuto.AddOption(autoStraightLine, autoStraightLine);
			  chooserAuto.AddOption(autoLineWithRot, autoLineWithRot);
			  chooserAuto.AddOption(autoLineWithTurn, autoLineWithTurn);
			  chooserAuto.AddOption(autoComplex, autoComplex);
			  frc::SmartDashboard::PutData("Auto Modes", &chooserAuto);
			}

			void Robot::RobotPeriodic() {}

			void Robot::AutonomousInit() {
			  timerAuto.Reset();
			  timerAuto.Start();
			  gyro.Reset();

			  hasSpin = false;
			  secondSpin = false;
			  thirdSpin = false;
			  firstAdvance = false;
			  secondAdvance = false;
			  thirdAdvance = false;

			}

			void Robot::AutonomousPeriodic() {

			  selectedAuto = chooserAuto.GetSelected();
			  SmartDashboard::PutString("Auto: ", selectedAuto);

			  if (selectedAuto == autoStraightLine){

			    //-----------------------------------------------------------------------
			    moveFor(3_s,firstAdvance);
			    //-----------------------------------------------------------------------

			  } else if (selectedAuto == autoLineWithRot){

			    //-----------------------------------------------------------------------
			    SmartDashboard::PutNumber("Angles: ", gyro.GetAngle());

			    moveFor(2.5_s,firstAdvance);
			    if (firstAdvance){
			      rotateFor(360,hasSpin);
			      if (hasSpin){
				moveFor(2.5_s,secondAdvance);
			      }
			    }
			    //-----------------------------------------------------------------------

			  } else if (selectedAuto == autoLineWithTurn){

			    //-----------------------------------------------------------------------
			    SmartDashboard::PutNumber("Angles: ", gyro.GetAngle());

			    moveFor(4_s,firstAdvance);
			    if (firstAdvance){
			      rotateFor(120,hasSpin);
			      if (hasSpin){
				moveFor(1.5_s,secondAdvance);
				if (secondAdvance){
				  microRotate(1_s,secondSpin);
				  if (secondSpin){
				    moveFor(3_s,thirdAdvance);
				  }
				}
			      }
			    }
			    //-----------------------------------------------------------------------

			  } else if (selectedAuto == autoComplex){}
			  else{}
			}

			void Robot::TeleopInit() {}
			void Robot::TeleopPeriodic() {}
			void Robot::DisabledInit() {}
			void Robot::DisabledPeriodic() {}
			void Robot::TestInit() {}
			void Robot::TestPeriodic() {}
			void Robot::SimulationInit() {}
			void Robot::SimulationPeriodic() {}

			#ifndef RUNNING_FRC_TESTS
			int main() {
			  return frc::StartRobot<Robot>();
			}
			#endif

	.. tab:: **Robot.h (Auto Only)** 

		.. code-block:: c++

			#pragma once

			#include "AHRS.h"
			#include <string>
			#include <opencv2/core/core.hpp>
			#include <opencv2/highgui/highgui.hpp>
			#include <opencv2/imgproc/imgproc.hpp>
			#include <chrono>
			#include <iostream>
			#include <frc/TimedRobot.h>
			#include <frc/smartdashboard/SendableChooser.h>
			#include <fmt/core.h>
			#include <frc/smartdashboard/SmartDashboard.h>
			#include <frc/Joystick.h> // Joystick Library
			#include <frc/motorcontrol/Talon.h> //Talon Library
			#include <frc/motorcontrol/MotorControllerGroup.h> //Motor Library
			#include <frc/drive/DifferentialDrive.h> // Drive Library
			#include <frc/Servo.h>
			#include <frc/Compressor.h>
			#include <frc/Solenoid.h>
			#include <frc/DoubleSolenoid.h>
			#include <frc/Timer.h>
			#include <frc/Encoder.h>
			#include <frc/DriverStation.h>
			#include "frc/smartdashboard/Smartdashboard.h"
			#include "ctre/Phoenix.h" //Falcon Library
			#include "networktables/NetworkTable.h" //Network Tables Library
			#include "networktables/NetworkTableEntry.h" //Network Tables Entry Library
			#include "networktables/NetworkTableValue.h" //Network Tables Value Library
			#include "cameraserver/CameraServer.h" //Camera Server Library
			#include "networktables/NetworkTableInstance.h" //Network Tables Instance Library
			#include "wpi/span.h"
			#include "cscore_oo.h"

			using namespace std;
			using namespace cv;
			using namespace frc;

			class Robot : public frc::TimedRobot {
			  public:
			    void RobotInit() override;
			    void RobotPeriodic() override;
			    void AutonomousInit() override;
			    void AutonomousPeriodic() override;
			    void TeleopInit() override;
			    void TeleopPeriodic() override;
			    void DisabledInit() override;
			    void DisabledPeriodic() override;
			    void TestInit() override;
			    void TestPeriodic() override;
			    void SimulationInit() override;
			    void SimulationPeriodic() override;

			    void moveFor(auto secondsMove, bool &loopController);
			    void rotateFor(auto angleMove,bool &loopController);
			    void microRotate(auto secondsMove, bool &loopController);

			  private:

			    frc::SendableChooser<std::string> chooserAuto;
			    const std::string autoDefault = "Default";
			    const std::string autoStraightLine = "Linea recta";
			    const std::string autoLineWithRot = "Linea con vuelta";
			    const std::string autoLineWithTurn = "Linea con giro";
			    const std::string autoComplex = "Secuencia compleja";
			    std::string selectedAuto;

			    AHRS gyro{SPI::Port::kMXP};

			    frc::Timer timerAuto;

			    bool hasSpin;
			    bool firstAdvance;
			    bool secondSpin;
			    bool thirdSpin;
			    bool secondAdvance;
			    bool thirdAdvance;

			    frc::Joystick m_stickONE{1};

			    WPI_TalonSRX motorFOUR = {1};      //Talon 4 -> ID 01 Talon
			    WPI_TalonSRX motorTHREE = {0};       //Talon 3 -> ID 00 Talon
			    WPI_VictorSPX motorTWO = {0};      //Talon 2 -> ID 00 Victor
			    WPI_VictorSPX motorONE = {1};     //Talon 1 -> ID 01 Victor


			    frc::MotorControllerGroup m_leftGroup{motorTHREE, motorFOUR};     //Grupo de motores -> Izquierdo
			    frc::MotorControllerGroup m_rightGroup{motorTWO, motorONE};    //Grupo de motores -> Derecho

			    frc::DifferentialDrive m_drive{m_leftGroup, m_rightGroup};  //Conduccion de tipo Arcade
			};

			void Robot::moveFor(auto secondsMove, bool &loopController){
			  if (loopController == false){
			    timerAuto.Start();
			    if (timerAuto.Get() < secondsMove){
			      m_drive.TankDrive(-0.3,0.3,false);
			    } else if(timerAuto.Get() >= secondsMove && loopController == false){
			      m_drive.TankDrive(0,0,false);
			      timerAuto.Reset();
			      timerAuto.Stop();
			      gyro.Reset();
			      loopController = true;
			    }
			  } 
			}

			void Robot::microRotate(auto secondsMove, bool &loopController){
			  if (loopController == false){
			    timerAuto.Start();
			    if (timerAuto.Get() < secondsMove){
			      m_drive.TankDrive(-0.4,-0.4,false);  //left Rotate
			    } else if(timerAuto.Get() >= secondsMove && loopController == false){
			      m_drive.TankDrive(0,0,false);
			      timerAuto.Reset();
			      timerAuto.Stop();
			      gyro.Reset();
			      loopController = true;
			    }
			  } 
			}

			void Robot::rotateFor(auto angleMove,bool &loopController){
			  if (loopController == false){
			    if (angleMove > 0){
			      if (gyro.GetAngle() < angleMove){m_drive.TankDrive(0.3,0.3,false);}
			      else if (gyro.GetAngle() >= angleMove){
				m_drive.TankDrive(0,0,false);
				gyro.Reset();
				loopController = true;
			      }
			    } else if(angleMove < 0){
			      if (gyro.GetAngle() > angleMove){m_drive.TankDrive(-0.3,-0.3,false);}
			      else if (gyro.GetAngle() <= angleMove){
				m_drive.TankDrive(0,0,false);
				gyro.Reset();
				loopController = true;
			      }
			    }
			  }
			}

Marte - Charged Up
------------------

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque sed ipsum erat. Integer neque velit, rutrum a ex ac, iaculis pulvinar lectus. Nullam auctor auctor facilisis. Phasellus leo odio, euismod id metus eu, blandit tempor lorem. Morbi id nunc ultricies nibh luctus hendrerit sit amet quis metus. Cras faucibus erat id pulvinar vulputate. Quisque sit amet auctor mauris. Sed eu orci nisi. Curabitur bibendum tristique urna quis placerat.

.. tabs::

	.. tab:: **Robot.cpp** 

		.. code-block:: c++

			#include "Robot.h"

			bool botonLB_toggle = false;
			bool botonB_toggle = false;

			void Robot::RobotInit() {
			  chooserAuto.SetDefaultOption(commLeaves, commLeaves);
			  chooserAuto.AddOption(chargeBalance, chargeBalance);
			  frc::SmartDashboard::PutData("Auto Modes", &chooserAuto);
			}
			void Robot::RobotPeriodic() {}

			void Robot::AutonomousInit() {
			  chooserAuto.SetDefaultOption(commLeaves, commLeaves);
			  chooserAuto.AddOption(chargeBalance, chargeBalance);
			  frc::SmartDashboard::PutData("Auto Modes", &chooserAuto);
			   timerAT.Reset();
			   timerAT.Start();
			   timerAT.Reset();
			   timerAT_Two.Reset();
			   timerAT_Two.Start();
			   timerAT_Two.Reset();}
			void Robot::AutonomousPeriodic() {
			  double gyroRoll = gyro.GetRoll();
			  auto timerDOB = timerAT.Get();//stod(units::to_string(timerAT.Get()));
			  double correctMul = 1.5;
			  cout << "Timer: " << units::to_string(timerDOB) <<endl;

			  selectedAuto = chooserAuto.GetSelected();
			  frc::SmartDashboard::PutString("Autonomo: ", selectedAuto);

			  if (selectedAuto == chargeBalance){

			    if (!isUp){
			      if (gyroRoll <= 12){ xAxis = -1; }
			      else{ isUp = true; timerAT.Start(); }
			    }else{
			      if (timerDOB > 1_s){
				if ((gyroRoll < 13 && gyroRoll > 4) || (gyroRoll < -3 && gyroRoll < -11)){
				  xAxis = 0.7 * getSymbol(gyroRoll);
				  gyroRollOld = gyroRollNew;
				  gyroRollNew = gyro.GetRoll();

				  if (gyroRollOld > 0 && gyroRollNew < 0){balanceCount += 1;}
				  if (balanceCount == 3 && balanceCount < 6){ xAxis =  0.6 * getSymbol(gyroRoll); }
				  else if (balanceCount >= 6){ xAxis =  0.55 * getSymbol(gyroRoll); }
				}else if (((gyroRoll >= 13 || gyroRoll <= 4) || (gyroRoll <= -11 || gyroRoll >= -3)) && timerDOB > 2_s){
				    brakeMotor();
				    xAxis = 0;
				}
			      }
			    }

			  }else if (selectedAuto == commLeaves){
			    timerAT.Start();
			    if (timerAT.Get() < 0.5_s){
			      xAxis = -0.7;
			    }else if(timerAT.Get() >= 0.5_s && timerAT.Get() < 1_s){
			      xAxis = 0.6;
			    }else if (timerAT.Get() < 3_s && timerAT.Get() >= 1_s){
			      xAxis = 0.9;
			    }else{
			      brakeMotor(); xAxis = 0; yAxis = 0;
			    }
			  }

			  motorDrive.ArcadeDrive(yAxis*0.8,xAxis*0.8);
			}

			void Robot::TeleopInit() {}
			void Robot::TeleopPeriodic() {
			  double powerMulti;

			  yAxis = stickOne.GetRawAxis(4);
			  xAxis = stickOne.GetRawAxis(1);

			  int povANGLE = frc::DriverStation::GetStickPOV(2,0);
			  bool botonB_pressed = stickTwo.GetRawButtonPressed(2);
			  double botonA = stickTwo.GetRawButton(1);
			  double botonB = stickTwo.GetRawButton(2);
			  double botonX = stickTwo.GetRawButton(3);
			  double botonY = stickTwo.GetRawButton(4);
			  double botonLS = stickTwo.GetRawButton(9);
			  double botonRS = stickTwo.GetRawButton(10);

			  double axisLift = stickTwo.GetRawAxis(1);
			  double axisHammer = stickTwo.GetRawAxis(4);

			  bool botonLB_pressed = stickOne.GetRawButtonPressed(5);
			  double triggerLeft = stickOne.GetRawAxis(2);     //Gatillo -> Reductor de velocidad
			  double triggerRight = stickOne.GetRawAxis(3);     //Gatillo -> Freno del robot

			  // --- Control de velocidades
			  if (triggerLeft){ brakeMotor(); xAxis = 0; yAxis = 0; powerMulti = 0;}    //Freno del chasis (Gatillo izquierdo)
			  else{
			    if (botonLB_toggle && botonLB_pressed){botonLB_toggle = false;}   //Cambio de velocidades
			    else if(botonLB_toggle == false && botonLB_pressed){botonLB_toggle = true;}

			    if (triggerRight){powerMulti = 0.65;}    //Reduccion de velocidad (Gatillo derecho)
			    else{                                             //Toggle de velocidades
			      if(botonLB_toggle == false){powerMulti = 0.6;}  //Por defecto 80% poder maximo
			      else{powerMulti = 0.8;}                         //Camnio a 100% poder maximo
			    }
			  }
			  // --- Control de velocidades

			  // --- Toggle de la garra (Presiona B -> Cambia estado)
			  if (botonB_toggle && botonB_pressed){ timerGC.Reset(); timerGC.Stop(); botonB_toggle = false; }
			  else if(!botonB_toggle && botonB_pressed){ timerGC.Reset(); timerGC.Stop(); botonB_toggle = true; }
			  // --- Toggle de la garra

			  // --- Control de la garra
			  if (botonB_toggle){ //Secuencia de cerrado de garra
			    timerGC.Start();
			    if (timerGC.Get() < 3_s){
			      falconTwo.SetNeutralMode(NeutralMode::Coast);
			      falconTwo.Set(ControlMode::PercentOutput,-0.40);
			    }else{
			      falconTwo.Set(ControlMode::PercentOutput,-0.10); //Cambia velocidad despues de 1.5 segundos
			    }
			  }else if (!botonB_toggle){ //Secuencia de apertura de garra
			    timerGC.Start();
			    if (botonX){falconTwo.Set(ControlMode::PercentOutput,-0.30);}
			    else if (timerGC.Get() < 0.3_s){
			      falconTwo.SetNeutralMode(NeutralMode::Coast);
			      falconTwo.Set(ControlMode::PercentOutput,0.20);
			    }else{
			      falconTwo.Set(ControlMode::PercentOutput,0);
			      falconTwo.SetNeutralMode(NeutralMode::Brake); //Frena despues de 0.3 segundos
			    }
			  }
			  // --- Control de la garra

			  // --- Control del elevador
			  if (axisLift < -0.5 /*povANGLE == 0*/){                                   //Sube elevador
			    motorFive.Set(ControlMode::PercentOutput,-0.4);
			  }else if (axisLift > 0.5 /*povANGLE == 180*/){                           //Baja elevador
			    motorFive.Set(ControlMode::PercentOutput,0.4);
			  }else{                                                //Frenar elevador
			    motorFive.Set(ControlMode::PercentOutput,0);
			    motorFive.SetNeutralMode(NeutralMode::Brake);
			  }
			  // --- Control del elevador

			  // --- Control del martillo
			  if (axisHammer > 0.3/*povANGLE == 90*/){                                         //Sube hammer
			      motorSix.Set(-0.8); cout << "Subiendo hammer" <<endl;
			  }else if (axisHammer < -0.3/*povANGLE == 270*/){                                 //Baja hammer 
			    motorSix.Set(0.8); cout << "Bajando hammer" <<endl;
			  }else{                                                     //Freno del hammer
			    motorSix.Set(0); motorSix.SetNeutralMode(NeutralMode::Brake);
			  }
			  // --- Control del martillo

			  // --- Control de mano
			  if (botonY){falconOne.Set(ControlMode::PercentOutput,-0.30); } //Sube mano/garra
			  else if (botonA){falconOne.Set(ControlMode::PercentOutput,0.30); } //Baja mano/garra
			  else { falconOne.Set(ControlMode::PercentOutput,0); falconOne.SetNeutralMode(NeutralMode::Brake); } //Detiene mano/garra
			  // --- Control de mano

			  // --- Sistema de conducción
			  motorDrive.ArcadeDrive(yAxis*0.6,xAxis*powerMulti); //Motores invertidos -> (Rotación aqui, Mov en X aqui)
			  // --- Sistema de conducción
			}

			void Robot::DisabledInit() {}
			void Robot::DisabledPeriodic() {}
			void Robot::TestInit() {}
			void Robot::TestPeriodic() {}
			void Robot::SimulationInit() {}
			void Robot::SimulationPeriodic() {}

			#ifndef RUNNING_FRC_TESTS
			int main() {
			  return frc::StartRobot<Robot>();
			}
			#endif

	.. tab:: **Robot.h** 

		.. code-block:: c++
		
			#pragma once

			#include "AHRS.h"
			#include "vector"
			#include <string>
			#include <iostream>
			#include "cscore_oo.h"
			#include "ctre/Phoenix.h"
			#include "photonlib/PhotonCamera.h"
			#include "cameraserver/CameraServer.h"

			#include <fmt/core.h>
			#include <frc/Timer.h>
			#include <frc/Joystick.h>
			#include <frc/TimedRobot.h>
			#include <frc/DigitalInput.h>
			#include <frc/DriverStation.h>
			#include <frc/motorcontrol/Talon.h>
			#include <frc/drive/DifferentialDrive.h>
			#include <frc/smartdashboard/SmartDashboard.h>
			#include <frc/smartdashboard/SendableChooser.h>
			#include <frc/motorcontrol/MotorControllerGroup.h>

			#include <cameraserver/CameraServer.h>

			#include "networktables/NetworkTable.h"
			#include "networktables/NetworkTableEntry.h"
			#include "networktables/NetworkTableValue.h"
			#include "networktables/NetworkTableInstance.h"

			using namespace std;

			class Robot : public frc::TimedRobot {
			  public:
			    void RobotInit() override;
			    void RobotPeriodic() override;
			    void AutonomousInit() override;
			    void AutonomousPeriodic() override;
			    void TeleopInit() override;
			    void TeleopPeriodic() override;
			    void DisabledInit() override;
			    void DisabledPeriodic() override;
			    void TestInit() override;
			    void TestPeriodic() override;
			    void SimulationInit() override;
			    void SimulationPeriodic() override;

			    void brakeMotor(); //Frena los motores de manera total
			    double getSymbol(double numHere);     //Regresa el simbolo de un numero (-1,0,1)
			  private:

			    frc::SendableChooser<std::string> chooserAuto;
			    const std::string chargeBalance = "Sube Charge Station";
			    const std::string commLeaves = "Sale de la comunidad";
			    std::string selectedAuto;

			    bool isUp; 
			    double gyroRollOld;
			    double gyroRollNew;
			    double balanceCount = 0;

			    frc::DigitalInput lsOne {1};
			    frc::DigitalInput lsTwo {2};
			    frc::DigitalInput lsThree {3};

			    frc::Joystick stickOne{1};
			    frc::Joystick stickTwo{2};

			    AHRS gyro{frc::SPI::Port::kMXP}; //Gyro NAVX

			    frc::Timer timerAT;       //Timer para controlar el auto
			    frc::Timer timerAT_Two;   //Timer para controlar el auto
			    frc::Timer timerGC;       //Timer para controlar la garra
			    frc::Timer timerDS;       //Timer para controlar la garra

			    WPI_TalonSRX motorFour = {3};     //Falcon 1 -> Izquierda
			    WPI_TalonSRX motorTwo = {4};      //Falcon 1 -> Izquierda
			    WPI_VictorSPX motorOne = {1};     //Victor 1 -> Derecha
			    WPI_VictorSPX motorThree = {2};   //Victor 2 -> Derecha

			    WPI_TalonSRX motorFive = {5};
			    WPI_TalonSRX motorSix = {8};
			    TalonFX falconOne = {6};
			    TalonFX falconTwo = {7};

			    frc::MotorControllerGroup motorLeftGroup{motorTwo, motorFour};     //Grupo de motores -> Izquierdo
			    frc::MotorControllerGroup motorRightGroup{motorOne, motorThree};    //Grupo de motores -> Derecho

			    double xAxis;
			    double yAxis;

			    frc::DifferentialDrive motorDrive{motorLeftGroup, motorRightGroup};  //Conduccion de tipo Arcade
			};

			void Robot::brakeMotor(){
			  motorOne.Set(0); motorTwo.Set(0);
			  motorThree.Set(0); motorFour.Set(0);
			  motorOne.SetNeutralMode(Brake); motorTwo.SetNeutralMode(Brake);
			  motorThree.SetNeutralMode(Brake); motorFour.SetNeutralMode(Brake);
			}

			double Robot::getSymbol(double numHere){
			  if (numHere != 0){
			    if (numHere < 0){return 1;}
			    else if (numHere > 0){return -1;}
			  }else{return 0;}
			}
