# robot


package org.usfirst.frc.team2972.robot;


import edu.wpi.first.wpilibj.CameraServer;
import edu.wpi.first.wpilibj.Compressor;
import edu.wpi.first.wpilibj.DoubleSolenoid;
import edu.wpi.first.wpilibj.Jaguar;
import edu.wpi.first.wpilibj.Joystick;
import edu.wpi.first.wpilibj.SampleRobot;
import edu.wpi.first.wpilibj.Timer;
import edu.wpi.first.wpilibj.smartdashboard.SmartDashboard;

/**
 * This is a demo program showing the use of the RobotDrive class.
 * The SampleRobot class is the base of a robot application that will automatically call your
 * Autonomous and OperatorControl methods at the right time as controlled by the switches on
 * the driver station or the field controls.
 *
 * The VM is configured to automatically run this class, and to call the
 * functions corresponding to each mode, as described in the SampleRobot
 * documentation. If you change the name of this class or the package after
 * creating this project, you must also update the manifest file in the resource
 * directory.
 *
 * WARNING: While it may look like a good choice to use for your code if you're inexperienced,
 * don't. Unless you know what you are doing, complex code will be much more difficult under
 * this system. Use IterativeRobot or Command-Based instead if you're new.
 */
public class Robot extends SampleRobot {
    Jaguar fl;
    Jaguar bl;
    Jaguar fr;
    Jaguar br;
    Jaguar elevator1;
    Jaguar elevator2;
    Joystick stick;
    DoubleSolenoid solenoidOne;
    Compressor compressor;

    public Robot() { 
    	System.load("/usr/local/lib/lib_OpenCV/java/opencv_java2410.so");
    	CameraServer camera = new CameraServer();
    	camera.setQuality(50);
    	camera.startAutomaticCapture("cam0");
    	fl = new Jaguar(0);
        bl = new Jaguar(1);
        fr = new Jaguar(2);
        br = new Jaguar(3);
        elevator1 = new Jaguar(4);
        elevator2 = new Jaguar(5);
        stick = new Joystick(0);
        solenoidOne = new DoubleSolenoid(0,1);
        
        compressor = new Compressor(0);
        compressor.setClosedLoopControl(true);
    }

    /**
     * Drive left & right motors for 2 seconds then stop
     */
    public void autonomous() {
        Timer.delay(2.0);		//    for 2 seconds
    }

    /**
     * Runs the motors with arcade steering.
     */
    public void operatorControl() {
    	double direction = 0;
    	double rotation = 0;
    	double strafePow = .5;
    	double elevatorPow = 0;
    	double flPow = 0;
    	double blPow = 0;
    	double frPow = 0;
    	double brPow = 0;
        while (isOperatorControl() && isEnabled()) {
        	
        	// forward & backward direction
        	if (stick.getRawAxis(3) > 0) // right trigger
        		direction = stick.getRawAxis(3);
        	else if (stick.getRawAxis(2) > 0) // left trigger
        		direction = -1 * stick.getRawAxis(2);
        	else
        		direction = 0;
        	
        	// rotation
        	rotation = stick.getX();
        	
        	// strafe
        	if (stick.getRawButton(6)) {
        		flPow = -strafePow;
            	blPow = strafePow;
            	frPow = strafePow;
            	brPow = -strafePow;
        	} else if (stick.getRawButton(5)) {
        		flPow = strafePow;
            	blPow = -strafePow;
            	frPow = -strafePow;
            	brPow = strafePow;
        	} else {
        		flPow = 0;
            	blPow = 0;
            	frPow = 0;
            	brPow = 0;
        	}
        	
        	SmartDashboard.putNumber("POV", stick.getPOV());
        	if (stick.getPOV() == 0) {
        		compressor.setClosedLoopControl(true);
        	} else if (stick.getPOV() == 180) {
        		compressor.setClosedLoopControl(false);
        	}
        	
        	// final motor direction calculation
        	fl.set(-direction + rotation + flPow);
        	bl.set(-direction + rotation + blPow);
        	fr.set(direction + rotation + frPow);
        	br.set(direction + rotation + brPow);
        	
        	// elevator control (X & Y)
        	if (stick.getRawButton(1)) {
        		elevatorPow = .8;
        		elevator1.set(elevatorPow);
            	elevator2.set(-elevatorPow*.5);
        	} else if (stick.getRawButton(4)) {
        		elevatorPow = -.8;
        		elevator1.set(elevatorPow*.5);
            	elevator2.set(-elevatorPow);
        	} else {
        		elevatorPow = 0;
        		elevator1.set(elevatorPow);
            	elevator2.set(-elevatorPow*.5);
        	}
//        	elevator1.set(elevatorPow);
//        	elevator2.set(-elevatorPow*.5);
        	
        	// claw
        	/*if (stick.getRawButton(2)){ 
            	solenoidOne.set(DoubleSolenoid.Value.kForward);
            }else if (stick.getRawButton(3)){
            	solenoidOne.set(DoubleSolenoid.Value.kReverse);
            }else{
            	solenoidOne.set(DoubleSolenoid.Value.kOff);
            }*/
        	
            Timer.delay(0.005);		// wait for a motor update time
        }
    }

    /**
     * Runs during test mode
     */
    public void test() {
    }
    
    
    
}
