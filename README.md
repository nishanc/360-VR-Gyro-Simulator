# Gyro Simulator for 360 VR
# In Unity
>This small script is using compass and accelorometer of your mobile phone to simulate the ativity of a gyroscope for the devices where the gyroscope sensor is not avalable.

![Animation](/Animation.gif?raw=true "360VR")
### How to use!
> Attach the script to your main camera


```sh
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GyroSimulator : MonoBehaviour {

    //public bool isInLandscape = true;
    //float yValue;
    float zValue;
    float zValueMinMax = 5.0f;
    Vector3 accelometerSmoothValue;
    //Smooth Accelerometer
    public float AccelerometerUpdateInterval = 1.0f / 100.0f;
    public float LowPassKernelWidthInSeconds = 0.001f;
    public Vector3 lowPassValue = Vector3.zero;
    void Start () {
        Screen.orientation = ScreenOrientation.LandscapeLeft;
        Input.compass.enabled = true;
    }
    //Update is called once per frame
    void Update () {
        //Set Z Min Max
        if (zValue < -zValueMinMax)
            zValue = -zValueMinMax;

        if (zValue > zValueMinMax)
            zValue = zValueMinMax;

        accelometerSmoothValue = LowPass();
		//yValue += accelometerSmoothValue.y;
        zValue += accelometerSmoothValue.z;

        transform.rotation = Quaternion.Slerp(
            transform.rotation,
            Quaternion.Euler(
                -zValue*5.0f,
                Input.compass.magneticHeading, 
                0
                ),
            Time.deltaTime*5.0f
            );
	}

	Vector3 LowPass()
	{
        Vector3 tilt = Input.acceleration;
        //if (isInLandscape)
        //    tilt = Quaternion.Euler(90, 0, 0) * tilt;
		float LowPassFilterFactor = AccelerometerUpdateInterval / LowPassKernelWidthInSeconds;
		lowPassValue = Vector3.Lerp(lowPassValue, tilt, LowPassFilterFactor);
		return lowPassValue;
	}
	void OnGUI () {
		GUI.TextArea(new Rect(100,0,100,100),Input.compass.magneticHeading.ToString());
        GUI.TextArea(new Rect(200, 0, 100, 100), Input.acceleration.z.ToString());
    }
}
```